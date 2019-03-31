.. title: Working with the French MESH
.. slug: working-with-the-french-mesh
.. date: 2018-12-19 09:00:34 UTC+01:00
.. tags: nlp
.. category: medical
.. status: private
.. link: 
.. description: 
.. type: text

What's the MESH
===============

What's the French MESH
======================

This is basically a fat XML without documentation(?)

What is the data structure 
==========================

The databricks spark-xml lib makes easy to transform an xml file into a sparks
dataframe.

.. code-block:: scala

    import com.databricks.spark.xml._
    
    spark.read.format("com.databricks.spark.xml")
    .option("rowTag", "DescriptorRecord")
    .load("fredesc2017.xml")
    .persist
    .registerTempTable("b").printSchema

The resulting dataframe schema is quite complicated.

.. code-block:: scala

    #root
    # |-- AllowableQualifiersList: struct (nullable = true)
    # |    |-- AllowableQualifier: array (nullable = true)
    # |    |    |-- element: struct (containsNull = true)
    # |    |    |    |-- Abbreviation: string (nullable = true)
    # |    |    |    |-- QualifierReferredTo: struct (nullable = true)
    # |    |    |    |    |-- QualifierName: struct (nullable = true)
    # |    |    |    |    |    |-- String: string (nullable = true)
    # |    |    |    |    |-- QualifierUI: string (nullable = true)
    # |-- Annotation: string (nullable = true)
    # |-- ConceptList: struct (nullable = true)
    # |    |-- Concept: array (nullable = true)
    # |    |    |-- element: struct (containsNull = true)
    # |    |    |    |-- CASN1Name: string (nullable = true)
    # |    |    |    |-- ConceptName: struct (nullable = true)
    # |    |    |    |    |-- String: string (nullable = true)
    # |    |    |    |-- ConceptRelationList: struct (nullable = true)
    # |    |    |    |    |-- ConceptRelation: array (nullable = true)
    # |    |    |    |    |    |-- element: struct (containsNull = true)
    # |    |    |    |    |    |    |-- Concept1UI: string (nullable = true)
    # |    |    |    |    |    |    |-- Concept2UI: string (nullable = true)
    # |    |    |    |    |    |    |-- _RelationName: string (nullable = true)
    # |    |    |    |-- ConceptUI: string (nullable = true)
    # |    |    |    |-- RegistryNumber: string (nullable = true)
    # |    |    |    |-- RelatedRegistryNumberList: struct (nullable = true)
    # |    |    |    |    |-- RelatedRegistryNumber: array (nullable = true)
    # |    |    |    |    |    |-- element: string (containsNull = true)
    # |    |    |    |-- ScopeNote: string (nullable = true)
    # |    |    |    |-- TermList: struct (nullable = true)
    # |    |    |    |    |-- Term: array (nullable = true)
    # |    |    |    |    |    |-- element: struct (containsNull = true)
    # |    |    |    |    |    |    |-- DateCreated: struct (nullable = true)
    # |    |    |    |    |    |    |    |-- Day: long (nullable = true)
    # |    |    |    |    |    |    |    |-- Month: long (nullable = true)
    # |    |    |    |    |    |    |    |-- Year: long (nullable = true)
    # |    |    |    |    |    |    |-- EntryVersion: string (nullable = true)
    # |    |    |    |    |    |    |-- SortVersion: string (nullable = true)
    # |    |    |    |    |    |    |-- String: string (nullable = true)
    # |    |    |    |    |    |    |-- TermNote: string (nullable = true)
    # |    |    |    |    |    |    |-- TermUI: string (nullable = true)
    # |    |    |    |    |    |    |-- ThesaurusIDlist: struct (nullable = true)
    # |    |    |    |    |    |    |    |-- ThesaurusID: array (nullable = true)
    # |    |    |    |    |    |    |    |    |-- element: string (containsNull = true)
    # |    |    |    |    |    |    |-- _ConceptPreferredTermYN: string (nullable = true)
    # |    |    |    |    |    |    |-- _IsPermutedTermYN: string (nullable = true)
    # |    |    |    |    |    |    |-- _LexicalTag: string (nullable = true)
    # |    |    |    |    |    |    |-- _RecordPreferredTermYN: string (nullable = true)
    # |    |    |    |-- TranslatorsScopeNote: string (nullable = true)
    # |    |    |    |-- _PreferredConceptYN: string (nullable = true)
    # |-- ConsiderAlso: string (nullable = true)
    # |-- DateCreated: struct (nullable = true)
    # |    |-- Day: long (nullable = true)
    # |    |-- Month: long (nullable = true)
    # |    |-- Year: long (nullable = true)
    # |-- DateEstablished: struct (nullable = true)
    # |    |-- Day: long (nullable = true)
    # |    |-- Month: long (nullable = true)
    # |    |-- Year: long (nullable = true)
    # |-- DateRevised: struct (nullable = true)
    # |    |-- Day: long (nullable = true)
    # |    |-- Month: long (nullable = true)
    # |    |-- Year: long (nullable = true)
    # |-- DescriptorName: struct (nullable = true)
    # |    |-- String: string (nullable = true)
    # |-- DescriptorUI: string (nullable = true)
    # |-- EntryCombinationList: struct (nullable = true)
    # |    |-- EntryCombination: array (nullable = true)
    # |    |    |-- element: struct (containsNull = true)
    # |    |    |    |-- ECIN: struct (nullable = true)
    # |    |    |    |    |-- DescriptorReferredTo: struct (nullable = true)
    # |    |    |    |    |    |-- DescriptorName: struct (nullable = true)
    # |    |    |    |    |    |    |-- String: string (nullable = true)
    # |    |    |    |    |    |-- DescriptorUI: string (nullable = true)
    # |    |    |    |    |-- QualifierReferredTo: struct (nullable = true)
    # |    |    |    |    |    |-- QualifierName: struct (nullable = true)
    # |    |    |    |    |    |    |-- String: string (nullable = true)
    # |    |    |    |    |    |-- QualifierUI: string (nullable = true)
    # |    |    |    |-- ECOUT: struct (nullable = true)
    # |    |    |    |    |-- DescriptorReferredTo: struct (nullable = true)
    # |    |    |    |    |    |-- DescriptorName: struct (nullable = true)
    # |    |    |    |    |    |    |-- String: string (nullable = true)
    # |    |    |    |    |    |-- DescriptorUI: string (nullable = true)
    # |    |    |    |    |-- QualifierReferredTo: struct (nullable = true)
    # |    |    |    |    |    |-- QualifierName: struct (nullable = true)
    # |    |    |    |    |    |    |-- String: string (nullable = true)
    # |    |    |    |    |    |-- QualifierUI: string (nullable = true)
    # |-- HistoryNote: string (nullable = true)
    # |-- NLMClassificationNumber: string (nullable = true)
    # |-- OnlineNote: string (nullable = true)
    # |-- PharmacologicalActionList: struct (nullable = true)
    # |    |-- PharmacologicalAction: array (nullable = true)
    # |    |    |-- element: struct (containsNull = true)
    # |    |    |    |-- DescriptorReferredTo: struct (nullable = true)
    # |    |    |    |    |-- DescriptorName: struct (nullable = true)
    # |    |    |    |    |    |-- String: string (nullable = true)
    # |    |    |    |    |-- DescriptorUI: string (nullable = true)
    # |-- PreviousIndexingList: struct (nullable = true)
    # |    |-- PreviousIndexing: array (nullable = true)
    # |    |    |-- element: string (containsNull = true)
    # |-- PublicMeSHNote: string (nullable = true)
    # |-- SeeRelatedList: struct (nullable = true)
    # |    |-- SeeRelatedDescriptor: array (nullable = true)
    # |    |    |-- element: struct (containsNull = true)
    # |    |    |    |-- DescriptorReferredTo: struct (nullable = true)
    # |    |    |    |    |-- DescriptorName: struct (nullable = true)
    # |    |    |    |    |    |-- String: string (nullable = true)
    # |    |    |    |    |-- DescriptorUI: string (nullable = true)
    # |-- TreeNumberList: struct (nullable = true)
    # |    |-- TreeNumber: array (nullable = true)
    # |    |    |-- element: string (containsNull = true)
    # |-- _DescriptorClass: long (nullable = true)

The folowing code extracts the first element of the term list. There is 54 one
of them in the 2017 mesh version. It is possible to get all of them by union.

.. code-block:: java

    sql("""with 
    tmp as (select 
      treenumberlist.TreeNumber as tree,
      descriptorui as descriptor,
      explode(conceptlist.concept.termlist.term[0]) as t
      from b) 
    select tree, 
      descriptor, 
      t.string, 
      t.termui, 
      t._ConceptPreferredTermYN, 
      explode(t.thesaurusidlist.thesaurusid) as dict 
    from tmp""")
    .show(30,true)
    // +--------------------+----------+--------------------+----------+-----------------------+----------------+
    // |                tree|descriptor|              string|    termui|_ConceptPreferredTermYN|            dict|
    // +--------------------+----------+--------------------+----------+-----------------------+----------------+
    // |[D03.633.100.221....|   D000001|          Calcimycin|   T000002|                      Y|  FDA SRS (2014)|
    // |[D03.633.100.221....|   D000001|          Calcimycin|   T000002|                      Y|      NLM (1975)|
    // |[D02.705.400.625....|   D000002|             Temefos|   T000008|                      Y|  FDA SRS (2014)|
    // |[D02.705.400.625....|   D000002|             Temefos|   T000008|                      Y|      INN (19XX)|
    // |[D02.705.400.625....|   D000002|             Temefos|   T000008|                      Y|     USAN (1974)|
    // |[D02.705.400.625....|   D000002|            Temephos|   T000007|                      N|      NLM (1996)|
    // |[J01.576.423.200....|   D000003|           Abattoirs|   T000009|                      Y|      NLM (1966)|
    // |[J01.576.423.200....|   D000003|    Slaughter Houses|T000901742|                      N|      NLM (2017)|
    // |[J01.576.423.200....|   D000003|     Slaughter House|T000901743|                      N|      NLM (2017)|
    // |[J01.576.423.200....|   D000003|     Slaughterhouses|   T000010|                      N|      UNK (19XX)|
    // |[L01.143.506.598....|   D000004|Abbreviations as ...|   T698652|                      Y|      NLM (2008)|
    // |       [A01.923.047]|   D000005|             Abdomen|   T000012|                      Y|      NLM (1966)|
    // |[C10.597.617.044....|   D000006|      Abdomen, Acute|   T000013|                      Y|      NLM (1966)|
    // |[C10.597.617.044....|   D000006| Abdomen chirurgical|fre0138059|                      N|French thesaurus|
    // |[C10.597.617.044....|   D000006|      Abdomen urgent|fre0138060|                      N|French thesaurus|
    // |           [C26.017]|   D000007|  Abdominal Injuries|   T000015|                      Y|      NLM (1966)|
    // |           [C26.017]|   D000007| Injuries, Abdominal|   T000014|                      N|      UNK (19XX)|
    // |           [C26.017]|   D000007|Traumatismes de l...|fre0037777|                      Y|French thesaurus|
    // |           [C26.017]|   D000007|Blessures abdomin...|fre0072220|                      N|French thesaurus|
    // |           [C26.017]|   D000007|Blessures de l'ab...|fre0103940|                      N|French thesaurus|
    // |           [C26.017]|   D000007| Lésions abdominales|fre0113338|                      N|French thesaurus|
    // |           [C26.017]|   D000007|Lésions de l'abdomen|fre0113337|                      N|French thesaurus|
    // |           [C26.017]|   D000007|Lésions traumatiq...|fre0103942|                      N|French thesaurus|
    // |           [C26.017]|   D000007|Lésions traumatiq...|fre0103941|                      N|French thesaurus|
    // |           [C26.017]|   D000007|Traumatismes abdo...|fre0047606|                      N|French thesaurus|
    // |       [C04.588.033]|   D000008| Abdominal Neoplasms|   T000016|                      Y|      NLM (1966)|
    // |   [A02.633.567.050]|   D000009|   Abdominal Muscles|   T000018|                      Y|      NLM (1966)|
    // |   [A02.633.567.050]|   D000009|    Muscle abdominal|fre0042987|                      N|French thesaurus|
    // |   [A02.633.567.050]|   D000009|Muscles de l'abdomen|fre0042988|                      N|French thesaurus|
    // |[A08.800.800.120....|   D000010|      Abducens Nerve|   T000019|                      Y|      NLM (1966)|
    // +--------------------+----------+--------------------+----------+-----------------------+----------------+
    // only showing top 30 rows



