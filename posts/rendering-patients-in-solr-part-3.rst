.. title: Rendering Patients in SolR - part 3
.. slug: rendering-patients-in-solr-part-3
.. date: 2018-12-13 21:29:51 UTC+01:00
.. tags: SolR medical
.. category: data engineering
.. link: 
.. status: private
.. description: 
.. type: text

The two previous attempts have failed to bring enough power and flexibility in
searching within patient history.

The current approach allows:

#. *highlighting* all the relevant information at the encounter level
#. mixing *logical operators* (OR,AND,NOT) between encounters
#. mixing *temporal order* between encounters


First the process of building cohorts changes to:

#. define the constraints on one encounter
#. eventually add patient level constraints
#. look for those matching encounters
#. loop 1. to 3. to define other encounters
#. add logical links between encounters/groups
#. add temporal links between encounters/groups

A constraint on a encounter might be:

- a date
- a delay
- a number
- a code
- a code and a number
- a code and a date
- a complex natural langage query
- a sequence of events
- a combination of all of them (AND,OR,NOT)

Constrained encounters can be then linked together. For example:

- encounterA: (37°C) => temp:37
- encounterB: (36°C) => temp:36
- encounterC: (35°C) => temp:35
- encounterA AND encounterB (where encounterA = encounterB is possible)
    - fq={!parent which="type:encounter"}(temp:37)&fq={!parent which="type:encounter"}(temp:36)
- encounterA OR encounterB
    - fq={!parent which="type:encounter"}(temp:37) OR (temp:36)
- encounterA BEFORE encounterB
    - fq={!parent which="type:encounter"}(temp:37 AND old.temp:36)&fq=(temp:36)
- (encounterA AND encounterB) BEFORE encounterC
    - fq={!parent which="type:encounter"}(temp:37)&fq={!parent which="type:encounter"}(temp:36)&fq={!parent which="type:encounter"}(temp:37 AND old.temp:35)&fq={!parent which="type:encounter"}(temp:36 AND old.temp:35)&fq={!parent which="type:encounter"}(temp:35)
- encounterA AND encounterB (where encounterA != encounterB)
    - fq={!parent which="type:encounter"}(temp:37 AND old.temp:36) OR (temp:36 AND old.temp:37)&fq={!parent which="type:encounter"}(temp:36)&fq={!parent which="type:encounter"}(temp:37)
- (encounterA AND encounterB) BEFORE encounterC (where encounterA != encounterB)
    - fq={!parent which="type:encounter"}(temp:37 AND old.temp:36) OR (temp:36 AND old.temp:37)&fq={!parent which="type:encounter"}(temp:36)&fq={!parent which="type:encounter"}(temp:37)&fq={!parent which="type:encounter"}(temp:37 AND old.temp:35)&fq={!parent which="type:encounter"}(temp:36 AND old.temp:35)&fq={!parent which="type:encounter"}(temp:35)

Sacrifices
==========
To enable the temporal/disjoint feature the documents are repeated in a
factorial way. This may lead to terrible situations.
Only the textual data should be stored. Moreover in each document, the
historical information should only be indexed and not stored.
Even by doing this the dimention of the index might be unreasonnable.
An other problem is the complexity to produce such documents: the serialization
to json might be a nightmare.

Less Ambitions
==============
By not enabling temporal/disjoint, what append's?

- encounterA: (37°C) => temp:37
- encounterB: (36°C) => temp:36
- encounterC: (35°C) => temp:35
- encounterA AND encounterB (where encounterA = encounterB is possible)
    - fq={!parent which="type:encounter"}(temp:37)&fq={!parent which="type:encounter"}(temp:36)
- encounterA OR encounterB
    - fq={!parent which="type:encounter"}(temp:37) OR (temp:36)
- (encounterA OR encounterB) AND encounterC
    - fq={!parent which="type:encounter"}(temp:37) OR (temp:36)&fq={!parent which="type:encounter"}(temp:35)
- (encounterA OR encounterB) NOT encounterC
    - fq={!parent which="type:encounter"}(temp:37) OR (temp:36)&fq=-_query_:"{!parent which=\"type:encounter\"}(temp:35)"

