.. title: Rendering Patients in SolR (Part-1)
.. slug: rendering-patients-in-solr
.. date: 2018-12-02 13:52:54 UTC+01:00
.. tags: SolR NLP 
.. status: private
.. category: data engineering
.. link: 
.. description: 
.. type: text

Patient medical records contain a broad variety of data such numeric, coding,
time-series or medical reports. It can be translated as a json document with a
nested structure. 

SolR allows to handle highly nested objects to be queried efficiently. It
provides pagination, and also faceting features wich are very powerful to
provide a patient centered search engine.

Patients in electronic health records:

.. image:: /images/patient_nested.jpg
    :width: 200%
    :align: center
    :alt: Patient Nested

Consider the two patients below:

.. include:: posts/patient.json
	:code: json

It yet possible to ask relevant questions to SolR:

.. code-block:: bash

   # Count and give me one patient having during the same visit:
   # - one XX1 code
   # - one report containing "popeye"
   curl http://localhost:8983/solr/patient/query -d '
   q=*:*
   &fq={!parent which="content_type_s:visite"}
       ccam_ss:XX1
   &fq={!parent which="content_type_s:visite"}
       {!complexphrase inOrder=true}
           doc_text_t:"popeye marche"~3
           AND doc_type_ss:APHP.CRH-H
   &fl=id
   &rows=1'

Using blockJoin queries has some limitations:
- it is not possible to highlight matching text from a parent or a child document
- it has some impact on performances

The particular model used in this post has some limitations:
- it is not possible to highlight matching text at the patient level
- it is not possible to play with a sequence of events, such of encounter
- queries are complicated

Let's see a other approach in a next post.
