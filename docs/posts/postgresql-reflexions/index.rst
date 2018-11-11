.. title: Postgresql Reflexions
.. slug: postgresql-reflexions
.. date: May 13, 2018
.. tags: postgresql
.. author: Nicolas Paris
.. link: 
.. description:
.. category: databases




After 4 years of postgresql management, I d'like to relate my experience.

Postgreql is incredible:

- its community is wonderful, the mailing list is very active and structured
- the recent years have been very productive for postgres, with many analytics
  improvements and perspectives
- it has one of the best support for transactions, and for example deal with
  DDL in transaction
- it's stable, we never had any crash in 4 years, or need to reboot the server
- it manage bot structured and unstructured data
- free text indexes and features are very advanced

However, sadly postgresql has some drawnbacks:

- it does not deal great with large volumne when comes analytics
- because it may be used for all batch ingesting, batch exporting, OLTP, dumps,
  analytics, the overall performances might be affected
- it does not scale horizontaly
- it has no fail over, or this lead to complex setup


Complementing postgresql with hadoop:

For this reason, it has been decided to externalize some responsibilities such
batch exporting and analytics to an other technology that scale horizontally:
hadoop. 

The good point is synchronizing data from postgresql to hive daily is easy
thanks to incremental sqoop import on hdfs, based on indexed timestamps in
postgreql.  This allows pushing the data from postgresql to hive ready to
analyse. The two tools interface well where hive does not handle sequence, or
triggers, and postgresql does not manage huge data historisation, and columnar
aggregation.








While postgreqsl does not handle well aggregations on bilions rows, it's children greenplum is supposed to.
 Let's envisage a greenplum post soon.

