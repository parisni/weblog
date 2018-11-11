.. title: Hive Merge
.. slug: hive-merge
.. date: May 13, 2018
.. tags: hive
.. author: Nicolas Paris
.. link: 
.. description:
.. category: databases


Hive introduced ACID statement: INSERT, UPDATE, DELETE & MERGE. It turns-out
HIVE 1.2.x is not optimised enough to support MERGE efficiently. An in depth
documentation can be found `there <https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions#HiveTransactions-Limitations>`_

.. END_TEASER

The code below :

- is idempotent in someway. Suppose for some reason, the `merge insert` statement
  fails. Then running again all statements won't be an issue since no update at
  all will append again. Same case if the `update` statement fails.
- Simple version: makes the assumption that any data both present in the
  `staging` table and the `target` table needs to be updated.
- SCD1 and SCD2 have slighly difference that are mentionned if existing.
- SCD2 has been tested (upsert version) on a large target table ( 1.3 Bilion
  rows * 100 columns) and a source table of 100 Milion rows. As a result, the
  total runtime was les than 3 hours. (50% resource on a 5 computer hadoop
  cluster).


0) Staging step
-------------------

Some data was put on hdfs in the <stg_table> to be merged with a target table.
This might be done by scoop incrementally. Also, this might be the whole table.
All the steps below will work the same in both case.


1) Preparation step
-------------------

The 3 table above are necessary for any of SCD1 or SCD2. They are
respectively the `transformation`, `insert`, `update` candidate tables.

.. code-block:: sql
        
   -- create transformation table
   CREATE TEMPORARY TABLE <trf_table> LIKE <target_table>

   -- create insert table
   CREATE TEMPORARY TABLE <ins_table> LIKE <target_table>

   -- create update table
   CREATE TEMPORARY TABLE <upd_table> LIKE <target_table>



2) Transformation step
----------------------

This step loads the trf table (`transformed`)  from the stg table (`staging`).
It adds the hash column, and is the place to add other transformations/columns
if needed.

.. code-block:: sql

   -- example without transformations
   INSERT INTO <trf_table>
   SELECT * , HASH(*) as hash
   FROM <stg_table>;

   -- example with transformations
   -- t1 and t2 are new columns added
   INSERT INTO <trf_table>
   SELECT * , HASH(stg.*) as hash
   FROM (SELECT * , t1(), t2() 
         FROM <stg_table>) as stg;

3) Dispatching step
-------------------

Simplified version:

.. code-block:: sql

   -- variation when you already know that rows are all different
   FROM <trf_table>
   INSERT INTO <upd_table> SELECT * WHERE <maj_date> IS NOT NULL
   INSERT INTO <ins_table> SELECT * WHERE <maj_date> IS NULL

Complete version:

.. code-block:: sql

   -- feed both update & insert table
   FROM (SELECT * 
         FROM  <trf_table> trf
         JOIN <target_table> targ
         ON ( 
         targ.<end_date> IS NULL 
         AND trf.<primary_key> = targ.<primary_key>
         ) 
         WHERE TRUE 
         AND trf.hash <> targ.hash) as updateable
   INSERT INTO TABLE <upd_table> SELECT * ;

   -- feed the insert table with new rows
   INSERT INTO <ins_table>
   SELECT *
   FROM <trf_table>
   WHERE TRUE
   AND <primary_key> NOT IN (
        SELECT <primary_key>
        FROM <target_table>
   );


4.1) Upsert version
-------------------

SCD 1:

Notice the delete is used in place of an update. The reason is an update cannot
join an other table and thus apply updates where needed.

.. code-block:: sql

   -- first delete update rows
   DELETE FROM <target_table>
   WHERE TRUE
   AND <primary_key> IN (
        SELECT <primary_key>
        FROM <upd_table>);

   -- second, insert all new rows
   INSERT INTO <target_table>
   SELECT * 
   FROM <ins_table>
   UNION ALL
   SELECT *
   FROM <upd_table>;

SCD 2:

.. code-block:: sql

   -- first update old rows
   UPDATE <target_table>
   SET <end_date> = current_date()
   WHERE TRUE
   AND <end_date> IS NULL
   AND <primary_key> IN (
        SELECT <primary_key>
        FROM <upd_table>);

   -- second, insert all new rows
   INSERT INTO <target_table>
   SELECT * 
   FROM <ins_table>
   UNION ALL
   SELECT *
   FROM <upd_table>;

4.2) Merge version
------------------

SCD 1:

.. code-block:: sql

   -- first update old rows
   MERGE INTO <target_table>
   USING 
   (
   SELECT null AS join_key, upd.* FROM <upd_table>
   UNION ALL
   SELECT <primary_key> AS join_key, ins.* FROM <ins_table>
   ) AS sub
   ON (sub.join_key = <target_table>.join_key
       AND <target_table>.<end_date> IS NULL)
   WHEN MATCHED 
        THEN UPDATE SET col1=sub.col1, ... , hash=sub.hash
   WHEN NOT MATCHED
        THEN INSERT VALUES (sub.col1, ... , sub.hash) ;

SCD 2:

.. code-block:: sql

   -- first update old rows
   MERGE INTO <target_table>
   USING 
   (
   SELECT null AS join_key, upd.* FROM <upd_table>
   UNION ALL
   SELECT null AS join_key, ins.* FROM <ins_table>
   UNION ALL
   SELECT <primary_key> AS join_key, ins.* FROM <upd_table>
   ) AS sub
   ON (sub.join_key = <target_table>.join_key
       AND <target_table>.<end_date> IS NULL)
   WHEN MATCHED  
        THEN UPDATE SET <end_date> = current_date()
   WHEN NOT MATCHED
        THEN INSERT VALUES (sub.col1, ... , sub.hash) ;

