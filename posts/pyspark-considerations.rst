.. title: PySpark Considerations
.. slug: pyspark-considerations
.. date: 2018-11-22 23:05:23 UTC+01:00
.. tags: python apache-spark
.. category: data engineering
.. link: 
.. description: 
.. type: text


Extracting raw data from hdfs
------------------------------

I have tested multiple ways to get data from hdfs. There is two situations:


The data fits in a local node RAM memory:
========================================

.. code-block:: python

   # will produce a local csv
   sql("select * from my_table where true")
       .toPandas()
       .to_csv("myLocalFile.csv", encoding="utf8")
   # otherwise spark shell keeps running
   exit(0)

.. code-block:: bash
   
    # run the python script:
    PYTHONPATH=my/python/path/prog.py pyspark --master yarn [...]


The dataset does not fit into RAM memory:
=========================================


.. code-block:: python

   # will produce some csv on hdfs
   sql("select * from my_table where true")
       .write
       .format("csv")
       .save("/output/dir/on/hdfs/")
   # otherwise spark shell keeps running
   exit(0)

.. code-block:: bash
   
    # run the python script:
    PYTHONPATH=my/python/path/prog.py pyspark --master yarn [...]
    # merge the files on the local filesystem
    hadoop fs -getmerge /output/dir/on/hdfs/ /desired/local/output/file.csv

