.. title: Yarn Reflexions
.. slug: yarn-reflexions
.. date: May 16, 2018
.. link: 
.. description:
.. tags: yarn, big-data
.. category: data ingineering

Here are some Apache Yarn consideratoins.

.. END_TEASER

Yarn is so great
-----------------

:Queue modifications: Some application needs extra cores/memory and need to be
                      boosted somehow? Yarns allows on the fly adjustement of
                      queues. In our case, we have nightly ETL and online users
                      the day. It's then easy to dispatch the resources where
                      needed, and without turning off running application.

:GPU management: Hadoop 3 will provide a new dimension in yarn responsibility.
                 Yarn will be able to allocate GPU resources for users. This
                 will be of incredible help for machine learning programs and
                 users to share and optimize access to them.

Yarn has some drawbacks
-----------------------

:Applications logs: They are stored locally. Same case for temporary files on
                    the local filesystem such map results. And there is no
                    trivial way to store point to hdfs. This is an essential
                    point when configuring the cluster, because the disk
                    allocated for storing yarn user files (hive, spark....) is
                    a real bottleneck. Depending on the cluster workload, keep
                    in mind having a minimum of 2TO for this stuff, per machine.

:Yarn superuser: There is now way to kill a process if you are not the owner of
                 the process. This makes things a bit more complex for an admin
                 to supervise such platform.
