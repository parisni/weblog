.. title: Solr joins thought Spark
.. slug: solr-joins-thought-spark
.. date: 2019-01-13 13:48:14 UTC+01:00
.. tags: spark
.. category: data engineering
.. link: 
.. status: private
.. description: Explore the benefit from bridging solr and spark
.. type: text

Context
=======

SolR is great to search within complexes documents. However it is weak to make
complexes joins between documents and lack for expressive languages. Combining
both solr and spark let envisage having best of two worlds.

Dataset
_______

+------------+----------+
| patient_id | visit_id |
+------------+----------+
| 1          | 1        |
+------------+----------+
| 1          | 2        |
+------------+----------+
| 2          | 1        |
+------------+----------+
| 2          | 2        |
+------------+----------+

R1
__

+------------+----------+
| patient_id | visit_id |
+------------+----------+
| 1          | 2        |
+------------+----------+
| 2          | 1        |
+------------+----------+
| 2          | 2        |
+------------+----------+

R2
__

+------------+----------+
| patient_id | visit_id |
+------------+----------+
| 1          | 2        |
+------------+----------+
| 2          | 2        |
+------------+----------+

Use case: Q1 AND Q2
===================

Let's say you have defined two queries Q1 and Q2 which have respectively R1 and
R2. Now you d'like to know which patients have both kind of visits.


.. code-block:: sql
	
	SELECT R1.patient_id, R1.visit_id 
	FROM R1 
	JOIN R2 USING (patient_id) 
	WHERE AND
	R2.visit_id != R1.visit_id


Use Case: Q1 OR Q2
==================

.. code-block:: sql
	
	(SELECT patient_id, visit_id FROM R1)
	UNION
	(SELECT patient_id, visit_id FROM R2)

Use Case: Q1 AND NOT Q2
=======================

.. code-block:: sql
	
	(SELECT patient_id, visit_id FROM R1)
	MINUS
	(SELECT patient_id, visit_id FROM R2)

Use Case: Q1 THEN Q2
=====================

Say we also have date along with data.


Say you want all Patients who had Q2 >1> Q1. You need to have the dates.

.. code-block:: sql

	SELECT R1.patient_id, R1.visit_id, R1.date 
	FROM R1 
	JOIN R2 USING (patient_id) 
	WHERE TRUE
	AND R2.visit_id != R1.visit_id 
	AND R2.date > R1.date + 1

	

Operations
==========

Given this formula: **(Q1 AND Q2) <2< Q3**


.. code-block:: sql

	WITH 
	Q1 as  (SELECT 1 as patient_id, 2 as visit_id, 3 as date 
		UNION ALL 
	       SELECT 2,1,2 
		UNION ALL 
	       SELECT 2,2,4),
	Q2 as  (SELECT 1 as patient_id, 2 as visit_id, 3 as date 
		UNION ALL 
	       SELECT 2,2,4),
	Q3 as  (SELECT 1 as patient_id, 2 as visit_id, 5 as date 
		UNION ALL 
	       SELECT 2,2,6),
	T1 as  (SELECT Q1.patient_id 
		FROM Q1
		JOIN Q2 USING(patient_id)
		WHERE TRUE
		AND Q2.visit_id != Q1.visit_id),
	T2 as  (SELECT Q1.patient_id
		FROM Q1
		JOIN Q3 USING(patient_id)
		WHERE TRUE
		AND Q3.visit_id != Q1.visit_id
		AND Q3.date > Q1.date + 2),
	T3 as  (SELECT Q2.patient_id
		FROM Q2
		JOIN Q3 USING(patient_id)
		WHERE TRUE
		AND Q3.visit_id != Q2.visit_id
		AND Q3.date > Q2.date + 2)
	(SELECT * FROM T1)
	INTERSECT
	(SELECT * FROM T2)
	INTERSECT
	(SELECT * FROM T2)


Given this formula: **(Q1 OR Q2) AND Q3**


.. code-block:: sql

	WITH 
	Q1 as  (SELECT 1 as patient_id, 2 as visit_id, 3 as date 
		UNION ALL 
	       SELECT 2,1,2 
		UNION ALL 
	       SELECT 2,2,4),
	Q2 as  (SELECT 1 as patient_id, 2 as visit_id, 3 as date 
		UNION ALL 
	       SELECT 2,2,4),
	Q3 as  (SELECT 1 as patient_id, 2 as visit_id, 5 as date 
		UNION ALL 
	       SELECT 2,2,6),
	T1 as  (SELECT Q1.patient_id 
		FROM Q1
		UNION
		SELECT Q2.patient_id
		FROM Q2),
	T2 as  (SELECT Q3.patient_id
		FROM Q3)
	(SELECT * FROM T1)
	INTERSECT
	(SELECT * FROM T2)


Given this formula: **Q1 AND Q2 AND Q3**


.. code-block:: sql

	WITH 
	Q1 as  (SELECT 1 as patient_id, 1 as visit_id),
	Q2 as  (SELECT 1 as patient_id, 2 as visit_id),
	Q3 as  (SELECT 1 as patient_id, 3 as visit_id),
	T1 as  (SELECT Q1.patient_id 
		FROM Q1
		JOIN Q2 USING(patient_id)
		WHERE TRUE
		AND Q2.visit_id != Q1.visit_id),
	T2 as  (SELECT Q2.patient_id 
		FROM Q2
		JOIN Q3 USING(patient_id)
		WHERE TRUE
		AND Q3.visit_id != Q2.visit_id),
	T3 as  (SELECT Q1.patient_id 
		FROM Q1
		JOIN Q3 USING(patient_id)
		WHERE TRUE
		AND Q3.visit_id != Q1.visit_id)
	(SELECT * FROM T1)
	INTERSECT
	(SELECT * FROM T2)
	INTERSECT
	(SELECT * FROM T3)


Given this formula: **Q1 <1< Q2 <1< Q3**


.. code-block:: sql

	WITH 
	Q1 as  (SELECT 1 as patient_id, 1 as visit_id, 3 as date),
	Q2 as  (SELECT 1 as patient_id, 2 as visit_id, 4 as date),
	Q3 as  (SELECT 1 as patient_id, 3 as visit_id, 5 as date),
	T1 as  (SELECT Q1.patient_id
		FROM Q1
		JOIN Q2 USING(patient_id)
		WHERE TRUE
		AND Q2.visit_id != Q1.visit_id
		AND Q2.date >= Q1.date + 1),
	T2 as  (SELECT Q2.patient_id
		FROM Q2
		JOIN Q3 USING(patient_id)
		WHERE TRUE
		AND Q3.visit_id != Q2.visit_id
		AND Q3.date >= Q2.date + 1)
	(SELECT * FROM T1)
	INTERSECT
	(SELECT * FROM T2)

Given this formula: **Q1 <1< Q2 AND NOT Q3**


.. code-block:: sql

	WITH 
	Q1 as  (SELECT 1 as patient_id, 1 as visit_id, 3 as date),
	Q2 as  (SELECT 1 as patient_id, 2 as visit_id, 4 as date),
	Q3 as  (SELECT 1 as patient_id, 3 as visit_id, 5 as date),
	T1 as  (SELECT Q1.patient_id
		FROM Q1
		JOIN Q2 USING(patient_id)
		WHERE TRUE
		AND Q2.visit_id != Q1.visit_id
		AND Q2.date >= Q1.date + 1)
	(SELECT * FROM T1)
	MINUS
	(SELECT * FROM Q3)

Isolating matching records
==========================

For each Qn it would be interesting to get the matching visit. This allows to
run the solr queries afterwards.

