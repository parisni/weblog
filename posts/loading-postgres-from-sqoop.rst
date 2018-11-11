.. title: Loading Postgres from Sqoop
.. slug: loading-postgres-from-sqoop
.. date: 2018-11-11 21:19:55 UTC+01:00
.. tags: postgresql, sqoop, hadoop, big-data
.. category: data engineering
.. link: 
.. description: 
.. type: text

PostgreSQL and Hadoop are complementary technologies. PostgreSQL outperforms
Hadoop to retrieve small amount of information from multiple tables. Haddop
outperforms PostgreSQL to transform multiple tables into simplfied and
aggregated tables. So, how to load PostgreSQL efficiently from Hadoop ?

.. END_TEASER

Sqoop as a bridge
=================

Hadoop stores data on its distributed filesystem: HDFS. At the end of the day,
the resulting procecced data is stored into a hdfs table and may need to be
loaded into a PosgreSQL table.

Copy is a efficient a robust PostgreSQL bulkloading function allowing to load
among from others CSV files. It is way faster than a bunch of parallel prepared
insert statements. 

ORC is a efficient and compressed Hadoop data format. It is both faster to
build and to read compared to CSV files.

As the bridge for Hadoop and relational databases, Sqoop manages all ORC, CSV,
COPY and INSERT methodologies. When dealing with PostgreSQL, there is no
perfect solution. 

Sqoop best approach
===================

There is two different scenario to load PostgreSQL.

:structured columns: Sqoop does not allow to use COPY together with ORC tables
                     but only with CSV. It splits them and run multiple COPY
                     statements from each part in parallel from HDFS. The CSV
                     format needs to be defined in the sqoop parameters. 


:unstructured columns: Text information may contain new lines. In this
                       situation, Sqoop is stuck when it splits the CSV into
                       multiple parts. Yet it is not possible to use the sqoop
                       direct mode, and the parallel inserts is the only way to
                       succeed. By chance, it is not necessar to transform the
                       result as CSV, and it is finally possible to exploit ORC
                       tables as-is.

Writing this post makes me thinking about a potential workaround to load
textual CSV from COPY by using only one mapper in this situation. Since COPY is
highly optimized it is possible that it won't degrade the performances while
making the overall process totally robust.
