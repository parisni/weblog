.. title: Spark Reflexions
.. slug: spark-reflexions
.. date: May 13, 2018
.. tags: spark
.. author: Nicolas Paris
.. link: 
.. description:
.. category: databases


Spark is a Swiss Army Knife of a hadoop stack. It makes glue between tools and is a de-facto interface for them.

Spark has a lot of advantages:

- it's a wonderfull connector: if you look at a way to connect to a tool, spark
  already has a connector for you. Special mention to csv: spark is also able
  to read multilined csv with quote, empty lines and special character in it.
- it can manipulate very huge dataset: in case the dataset does not feat in
  memory, the spark partitionning feature allows spliting the tasks in working
  pipelines
- its parallel feature can take advantage of a hadoop cluster, but is a great
  solution for a laptop too
- the scala spark-shell is really handy for development. It's auto-completion
  features are superior to any shell I've dealt with before
- it has SQL syntax, SPARKQL compliance, and also machine learning distributed
  implementation. 
- it can be programmed in scala, python, R, java and julia.


Spark as some disadvantages:

- time of writing, spark does not support hive ACID tables. The workaround is
  to do a major compaction of the table before reading it with spark. This
  means it cannot yet be integrated into a hive ETL process based on ACID
  tables.
- spark programming needs a lot of skills. It is then not a good choice for
  intermediate enterprise in comparison to hive programming. Again, hive ETL
  looks a better fit for a maintainability reason.
