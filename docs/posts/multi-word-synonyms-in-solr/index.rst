.. title: Multi-word synonyms in SolR
.. slug: multi-word-synonyms-in-solr
.. date: 2018-12-14 17:13:36 UTC+01:00
.. tags: SolR
.. category: data engineering
.. link: 
.. status: private
.. description: 
.. type: text

Medical domain has multiple ways to tell about the same thing. It becomes handy
to use and maintain muliword synonyms thesauri.

SolR recently released a built-in solution to handle multi-word synonyms and I
have tested it successfully:

- synonyms are computed at query time **only**.
- using multi-word synonyms are only compatible with simple/lucene/eDismax
  query parsers right now. **complexphrase** is broken with such dictionary.
- multi-word synonyms are supposed to be used **before stemming**
- adding new synonyms needs to delete the collection and reindex it. (to be confirmed)

Some questions remains:
1) How many synonyms can solr handle and still perform well ?
2) How to make synonyms addition/modifications without rebooting ?
3) Which is the better query parser together with multi-word synonyms ?

The simple query parser
=======================

Given this field: *text:"say me bye or say hello world"*
And the given configuration:

.. code-block:: xml
   
	<example>

- one word **text:(hello)** matches **"say me bye or say <em>hello</em> world"**
- one regex **text:(/hel.**/)** matches **"say me bye or say <em>hello</em> world"**
- not one word **text:(hello)** matches **"say me bye or say <em>hello</em> world"**
- one tailing wildcard **text:(**o)** matches **"say me bye or say <em>hello</em> world"**
- one fuzzy word **text:(word~)** matches **"say me bye or say hello <em>world</em>"**
- two words **text:(hello AND bye)** matches **"say me <em>bye</em> or say <em>hello</em> world"**
- two words exact **text:("hello world")** matches **"say me bye or say <em>hello</em> <em>world</em>"**
- two words with distance **text:("say world"~2)** matches **"say me bye or <em>say</em> hello <em>world</em>"**
- one word AND two words with distance **text:(bye AND "hello world"~2)** matches **"say me <em>bye</em> or say <em>hello</em> <em>world</em>"**
- two words with distance AND two words with distance **text:("say bye"~2 AND "say world"~2)** matches **"<em>say</em> me <em>bye</em> or <em>say</em> hello <em>world</em>"**
- one multi-word synonym: **text:("see you soon"~2  AND "hello world"~2)** matches **"say me <em>bye</em> or say <em>hello</em> <em>world</em>"**

Note: The simple query parser has some limations compared to the complexphrase parser:

- it only keeps order for exact phrases
- fuzzy, joker are not allowed for phrases

However it has some advantages:

- it allows to define multiple groups of words
- it allows regex on one word

How to handle negation
======================
SolR allows to look for *the absence* of a word or phrase, but not for
negations of them.
One solution I found is to exploit the multi-word synonyms, by linking all the
various ways to express negation to the *neg* word. Then it is possible to ask
for the absence of a negation.

.. code-block:: bash

   never had,never had any, has no, not, no anymore => neg

This can be used in this way:

- *"text": "I never had any trouble"*
- searching for people with trouble: *text:((trouble) AND NOT ("neg trouble"))*

Generalisation of negation
--------------------------
For a given *simple full-text* low level filter such a word or a phrase:

- *simple*           =>  *(simple AND NOT "neg simple")*
- *"very simple"*    =>  *("very simple" AND NOT "neg very simple)*
- *less simple*      =>  *( (less AND NOT "neg less") OR (simple AND NOT "neg simple) )*
- *less AND simple*  =>  *( (less AND NOT "neg less") AND (simple AND NOT "neg simple) )*

Simplifying interface for user
------------------------------
The more user friendly interface in my mind would allo people to write group of
words and articulate them with logical operator such AND/OR/NOT.
You don't want them to manage phrase and word too. So when a user look for:

- *see you soon* => *"see you soon"*

They might check for ordered and compacted word, but by default we the tolerate some distance:

- *see you soon* => *"see you soon"* => *"see you soon"~4*

In order to remove negated pattern, we apply the above method:

- *see you soon* => *"see you soon"* => *"see you soon"~4* =>  *"see you soon"~4 AND NOT "neg see you soon"~4*

People might add other groups and articulate them together:

- *("see you soon"~4 AND NOT "neg see you soon"~4) NOT ("an other example"~4 AND NOT "neg an other example"~4)*

The above example would match a text containing *see you soon* and not
containing *an other example*. By the way any multi-word synonym would be
translated. This also means the user not able to use jocker or fuzzy search.
However, the jocker can be replaced with the *stemming* process, which has the
advantage of not breaking the performances in case of very narrow joker query
and also being transparent for the end user.
There is also a possible replacement for fuzzy feature transparent to user.

Fuzzy search with synonyms
==========================
Word Embedding offers the opportunity to freely produce typo synonyms. I have
tested succesfully a quite large list of them (~200k entries) and the
performances where not impacted. So most common error are transparently
integrated to end users.

Dated, Delay and other structured informations
==============================================
The simple query parser let add some structured informations within the
free-text to be queried. For example the below enriched text allows:

- *"text":"delay0002 dateCreat20181214 my example free-text"*

- *text:([delay0001 TO delay0003] AND [dateCreat201812 TO NOW] AND "example")*
  this makes possible to filter based on dates.

It is also possible to add many coded informations within the text to be
queried as structured data with full-text structured capabilities. This will be
very useful to get NLP pipelines results.

Dealing with multivalued fields (MV)
====================================

While the MVs look temptating for storing multiple occurence of the same
concept, they are not a good choice when dealing with full text queries. Indeed
the multiple values are not considered as independant values but as a whole
with some defined distance between values.

So how to modelize multiple occurences of the same concept within SolR ? One
alternate solution is to use multiple single fields for textual datatype. For
example it the encounter has two physician notes both with a adverse event
section, the resulting encounter document will have two fields "pn.adverse1"
and "pn.averse2".
This implies that when the user asks for something present into this section,
the resulting query should be modified accordingly

- *"pn.adverse1":"hello world"*
- *"pn.adverse2":"my example"*

- *"pn.adverse:(hello AND example)"* will be replaced by:
- *"pn.adverse1:(hello AND example) OR pn.adverse2:(hello AND example)"*

This makes also possible to mix full text queries with structured queries based
on the same field. 
Also there is some drawbacks. The replacement mecanism makes mandatory to know
in advance the maximum number of fields of every documents.
An other drawback is when the user wants to look into every section of the same
document. One solution is to copyField 

Conclusion
==========
The final method offers:

- multi-word synonyms
- monoword synonyms
- monoword typos (fuzzy match)
- stemming
- negation
- AND/OR/NOT operators

Still some aspects are missing:

- no weighted queries (but not needed for my use case)
- no multivalued textual fields (this parser does not make difference between them)

This lets envisage a simple but powerful interface. Let's now see how to
transform a medical relational database and populate SolR.

