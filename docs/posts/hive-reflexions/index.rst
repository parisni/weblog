.. title: hive Reflexions
.. slug: hive-reflexions
.. date: May 10, 2018
.. tags: hive
.. author: Nicolas Paris
.. link: 
.. description:
.. category: databases



Hive has several advantages over other Datawarehouse solutions.

1. it is java based. This allows it to take advantage of UDF. The best and the
   moste complicate way to write them as generic UDFs. The other important
   aspect is to make them `blessed UDF` by mean they are global and accessible
   to any user. Generic UDF can produce any hive type from integer to complexe
   map structure.

2. it can read/write into hbase and take advantage of a key-value store.

3. it is ACID compliant, and provide both insert/update. However, this new
   feature is not well integrated with other tools such apache spark or
   facebook presto.

4. it supports multiple level of performance tunning such partitionning,
   bucketting, bloom filters, predicate pushdown.


Still hive has several disavantages:

1. lack or support for sequences. The workaround right now is to either use a
   UUID - this has several drawbacks such performances/storage issue. A second
   approach is to use the `sequence_nb + (row_number() over())`  function where
   `sequence_nb` is maintained into a table - the count of the inserted rows
   need to be added to the sequence. The last solution is a long, but it cannot
   be used concurrently. The solution cannot be used in production.



hive configuration tips
-----------------------

Hive can have OOM. Then this parameter might help
- `set hive.exec.orc.memory.pool = 1.0`

Predicate Push Down is better when INSERT STMT use a SORT BY 
"orc.bloom.filter.columns"="col1,col2"

It is also possible to update the bloom filter by running:
`ANALYZE TABLE Table1 COMPUTE STATISTICS FOR COLUMNS;`

- `show progress bar in beeline <https://medium.com/@anishekagarwal/apache-hive-beeline-progress-bar-fd57c29da65d>`_
