.. title: Hbase Reflexions
.. slug: hbase-reflexion
.. date: May 13, 2018
.. link: 
.. description:
.. tags: phoenix, spark, hive, solr, big-data
.. category: data ingineering


hbase looks a powerfull tool in complement of hive. While hive suits well
for ETL and data historisation, it cannot offer sub-second access to thousand
of concurrent users.

.. END_TEASER

Hbase comes with a powerful compagnion aka Phoenix that provides many features,
while keeping the hbase features intact. Phoenix provides a jdbc driver to
hbase. This distinction adds many improvements:

1. it offers sql, joins, secondary indexes, transactions, sequences on top of hbase.

2. it simplifies hive, spark, solr, and general programming access to hbase
   data.

JDBC overview
-------------

jdbc access can be done with `kerberos <https://community.hortonworks.com/questions/47138/phoenix-query-server-connection-url-example.html>`_ on the form 

.. code-block:: java

   "jdbc:phoenix:thin:url=<scheme>://<server-hostname>:<port>;authentication=SPNEGO;principal=my_user;keytab=/home/my_user/my_user.keytab"

Phoenix connection thought JDBC needs :
- a jdbc jar file 
- the hbase-site.conf 
- a kerberos ticket

Phoenix does not provide a way for connection pooling. The costly part of the
connection is the hbase link. However it is cached within the region servers
and a new phoenix jdbc connection can be rebuild for every query since it is a
lightweight object.

Hive integration
----------------

Phoenix tables can be mounted into hive thanks to a `recent plugin`_. In
comparaison to hbase plugin, this allows fast join to hive table (to be
tested). While this plugin needs phoenix 4.8.0+ HDP ships with phoenix 4.7.0.
However HDP Phoenix is a fork of Phoenix, and it integrates this feature.

Because phoenix allows to mount a hbase table, hive a phoenix or a hbase table,
there is many ways to make hive querying hbase. Moreover hive can create a
regular table stored as a hbase or phoenix table wich is slightly different.
The best way is to mount phoenix as a hive external table. First advantage is
*phoenix* allows salted tables by mean there is no need to manually handle the
key to be well distributed. Second, is loading directly into *phoenix* allows
it to manage its secondary indexes and also some other tools such *solr*. 

Finally, hive can access an external *phoenix* table and allow both upsert and
select. The plugin also provides a way to delete/update from hive, however I
haven't been able to reproduce yet since transactional tables are only
compatible with orc based tables.

Spark integration
-----------------

Spark can read and write from phoenix `here on SOF <https://stackoverflow.com/questions/40329968/apache-spark-ways-to-read-and-write-from-apache-phoenix-in-java>`_ or `here on official <https://phoenix.apache.org/phoenix_spark.html>`_. The good point is it can be done from scala or python.

Solr integration
----------------

Solr integration with hbase is a good idea: hbase contains the truth and solr allows fast lookup to discover it. However, setting up solr on top of hbase is a pain. Phoenix simplifies drastically that :
`here <https://nicholasmaillard.wordpress.com/2014/12/27/phoenix-to-solr-in-20-minutes/>`_


.. _recent plugin: https://phoenix.apache.org/hive_storage_handler.html
