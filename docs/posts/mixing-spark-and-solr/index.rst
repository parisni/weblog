.. title: Mixing Spark and SolR
.. slug: mixing-spark-and-solr
.. date: 2018-12-17 22:53:39 UTC+01:00
.. tags: spark, solr
.. category: data engineering
.. link: 
.. status: private
.. description: 
.. type: text

The spark-solr library makes it possible to interact with SolR-Cloud - it is
not compatible with SolR standalone. It allows both *reading* and *writing*
within SolR.

.. END_TEASER

Reading from SolR
=================

The library implements the *export* features which is fast to retrieve
DocValues enabled columns.

.. code-block:: scala

	// configure the access
	val options = Map( "collection" -> "gettingstarted", "zkhost" -> "localhost:9983")
	// use export to boost the retrieval
	// choose fields and the query
	spark.read.format("solr").options(options).option("request_handler", "/export").option("fields","id").option("query","id:/.*.pdf/").load.count


Writing from Spark
==================

.. code-block:: scala

	csvDF.write.format("solr").options(options).mode(org.apache.spark.sql.SaveMode.Overwrite).save


Creating JSON from Spark
========================

Many useful sql functions to build json from spark exist. So far, I have been
using *to_json*, *named_stuct*, *map*, *map_from_entries* and *concat_map* with some success.


**Generate a json object with zero to many items**, from zero to many rows:

.. code-block:: sql

	with
	tmp as (select 1 id,'sec1' sec,'a' cont 
		union all 
		select 1,'sec2', 'b' 
		union all 
		select 2, 'sec2','c')
	select id, to_json(map_from_entries(collect_list(struct(sec, cont)))) as t 
	from tmp 
	group by id;

	-- +---+-----------------------+
	-- |id |t                      |
	-- +---+-----------------------+
	-- |1  |{"sec1":"a","sec2":"b"}|
	-- |2  |{"sec2":"c"}           |
	-- +---+-----------------------+

**Add an element within the resulting object**, use *concat_map* to merge them. By
using the coalesce function to replace null with empty map, this avoids the
collateral effect of nulls that would result into null in any case (null + x =
null)

.. code-block:: sql

	with
	tmp as (select 1 id,'sec1' sec,'a' cont 
		union all 
		select 1,'sec2', 'b' 
		union all 
		select 2, 'sec2','c')
	select id, to_json(
			concat_map(
				coalesce(map("id",id),map()), 
				coalesce(map_from_entries(
					collect_list(
						struct(sec, cont))),map()))) as t 
	from tmp 
	group by id

	-- +---+---------------------------------+
	-- |id |t                                |
	-- +---+---------------------------------+
	-- |1  |{"id":"1","sec1":"a","sec2":"b"}|
	-- |2  |{"id":"1","sec2":"c"}           |
	-- +---+---------------------------------+

**Create nested objects** and place them into one parent object. The use of a
named_struct allows to nest objects within a first parent object: 

.. code-block:: sql

	with 
	secs as (select 1 as p_id, 'sec1' as sec, 'hello' as cont 
		union all
		select 1 as p_id, 'sec2' as sec, 'world' cont),
	tmp as (select p_id, map_from_entries(collect_list( struct(sec, cont) ) ) as t 
		from secs 
		group by p_id)
	select to_json( named_struct('id', p_id, '_childDocuments_', collect_list(t))) as t 
	from tmp 
	group by p_id

	-- +-------------------------------------------------------------+
	-- |t                                                            |
	-- +-------------------------------------------------------------+
	-- |{"id":1,"_childDocuments_":[{"sec1":"hello","sec2":"world"}]}|
	-- +-------------------------------------------------------------+

Good practice for building json from SQL within spark
=====================================================

Since it is possible to create json elements and concatenate them into objects
with joins, it improves readibility to create each kind of element step by step
and at the end concatenate them into larger objects. This should not degrade
the performance thanks to the spark lazy evaluation design.

As a result, it is possible to begin with encounter information one by one.
Sections, then laboratories, medications and so on. Then it is possible to
build the encounter by combining all this stuff. Afterwards comes the patient
related informations. At the end, it is possible include the encounters as
nested childrens within the patients.

By using this design it will be quite easy to enrich any element, or moove some
into other, repeat, rename, remove them. It will be also easy to debug. The
overall prototype would look like the below steps:

.. code-block:: sql

	-- encounter labs step
	-- encounter diags step
	-- encounter sections step

	-- patient step and nesting encounters into them
	-- good point is: named_struct automatically removes empty fields
	with 
	secs as (select 1 as p_id, 'sec1' as sec, 'hello' as cont 
		union all
		select 1 as p_id, 'sec2' as sec, 'world' cont),
	tmp as (select p_id, map_from_entries(collect_list( struct(sec, cont) ) ) as t 
		from secs 
		group by p_id)
	select to_json( named_struct(
				'id', patient_id, 
				'birthDate', birthDate, 
				'_childDocuments_', collect_list(t)
				)
			) as t 
	from patients
	left join encounters using (patient_id)
	group by patient_id

Limitations of spark-solr lib
=============================

- it does not support solr standalone (only solr-cloud)
- it does not handle multi-valued for childDocuemnt yet (fixed with a patch)
- it does not handle two levels of childDocuments yet
