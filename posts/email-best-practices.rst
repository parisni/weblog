.. title: Email Best Practices
.. slug: email-best-practice
.. date: Sep 03, 2018
.. tags: postgresql, archlinux
.. author: Nicolas Paris
.. link: 
.. description:
.. category: email


I will post here all best practices about writing emails. A lot of useful
information can be found `there <https://www.ietf.org/rfc/rfc3676.txt>`_

.. END_TEASER

Writing email
-------------
#. Choose an email object

   For example this pattern is great: **[Action] Topic**.
   **Action** to help your recipient to understand what you want from him.
   **topic** is a small synthesis of the topic in which the action is supposed to happen.
   This pattern has been advised by Michael Kahn.

#. Keep the body narrow

   The email should not size more than 72 character width. This improve
   readability specially on small screen. In vim, the width can be setup, and
   the shorkey **g+q** on a visual selection makes things easy.

.. TEASER_END

Reply to email
--------------
#. Reply below your recipient

   Bottom Posting (BP) is answering bellow the previous email as opposite to
   Top Posting (TP) aka gmail way of doing. BP has many advantages over TP. BP
   allows to read the emails in the natural order ; specially for people who
   discover the thread. `more information here <http://www.idallen.com/topposting.html>`_
   Keep an empty row between the previous answer and yours for readability.

#. Remove information

   Remove the recipient signature, out of context content. In particular, do
   not keep the recipient email within the email body for security reasons.

#. Append your signature

   A signature is generally on the bottom of the email, preceded by a row containing: *--*

#. Sign the email

   Sign the email with your AES private key as a proof of your identity.

#. Mask your mailing system

   Email can store the **User-agent** field. This can be found in the header of
   the email and may share precious information such your operating system and
   its version. eg: **User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64; rv:52.0) Gecko/20100101 Thunderbird/52.3.0**

Mailing List
------------
#. Email the mailing list **only**

   Remove the previous writer. In any cases, he will receive the email since he
   subscribed to the list.

#. Use a dedicated alias

   Mailing list are usually public and this you will probably end up with spam.
   A good trick is to use an alias combined with a rule such: "message directed
   to the alias goes into the trash". Riseup.net provides alias email.

