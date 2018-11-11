.. title: PrestoDB reflexions
.. slug: prestodb-reflexions
.. date: May 13, 2018
.. tags: prestoDB
.. author: Nicolas Paris
.. link: 
.. description:
.. category: databases


Prestodb is a cool distributed engine over hadoop. It can be compared to hive
LLAP, impala or apache DRILL for fast in-memory OLAP analysis and data
discovery.

.. END_TEASER

The main disadvantages it has right now are:

- it is not ACID compliant and far from being: this means tables in presto
  shall be created from scratch for analytics. Because we are speaking about
  big data, this implies copying
- it does not and never will support hive views: this would have been an
  opportunity to create pseudo-acid workflows
- it does not integrate with hbase
- it does not integrate with YARN, or at least the attempt looks discontinued
  on github.

The main advantage it has over hive LLAP are:

- many more build-in functions
- has been proven to scale well with tons of users
- integrates with RDBMS such postgres or NOSQL stores such mongodb



Sor far, the best feet for our internal arhitecture is to go with hive llap in
replacement to prestodb. This will brings us the ability to better integrate
with hbase/phoenix/solr from one part, and also give us the ability to query
ACID version of the DWH without data deduplication.
