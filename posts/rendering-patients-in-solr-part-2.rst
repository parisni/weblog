.. title: Rendering patients in SolR (part-2)
.. slug: rendering-patients-in-solr-part-2
.. date: 2018-12-04 00:43:57 UTC+01:00
.. tags: SolR, medical, search engine
.. category: data engineering
.. status: private
.. link: 
.. description: 
.. type: text

A patient search engine needs to meet several criteria:
#. blazing fast
#. handle both structured and unstructured data
#. for free text retrieval: include NLP pipelines results
#. define complex sequence of events, on patient, and/or encounter level
#. highlight results


The `previous post <https://parisni.github.io/weblog/posts/rendering-patients-in-solr>`_ was
a first attempt to modelize a patient in SolR. While it offered solution
for the three first crieteria, it was ineficient to provide event based
reasonning and also highlighting results. The below method tries to answer more
elegantly the question.


The idea is to modelize the patient as a list of nested encounters with the
last encounter beeing the first and vice-versa. Each encounter object being a
flat and denormalized document. The design allows to reason sequence of events
and also to highlight the part of the texts that match the query.

.. image:: /images/encounter_nested.jpg
    :width: 200%
    :align: center
    :alt: Encounter Nested

The denormalization has several advantages in SolR such speading up the queries
and also simplifying them. By combining multivalued texts fields and path based
text values the design pattern offers power and simplicity. An other aspect of
denormalization is to repeat the patient demographic information in each
encounter, to enable filtering on this level, and also faceting on patients
details.


Having nested encounters allows reasoning on sequence of event by using
blockJoin. Indeed this offers the opportunity to add query filter based on
childs. The idea is to add a level in each encounter by counting the number of
days from the last encounter. It is yet possible to ask for:
A patient having a breast cancer and whoom had a chirurgical mamal implant
within the 2 years.


One drawback of this design is it does not bring back patients but encounters.
One solution is to use faceting capabilities to count the number of unique
patients within the cohort.

EDIT: The method has been disapointing:

#. a parent object shall have a different nature than childs. It is then not
possible make a be a parent at any level of the chain.
#. only a parent object can be highlighted. While it is possible to reason on
childs content, it is not possible to highlight them

Let's invent a better method
