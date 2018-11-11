.. title: Mutt & Sidebar
.. slug: mutt-sidebar
.. date: Aug 26, 2018
.. tags: mutt
.. author: Nicolas Paris
.. link: 
.. description:
.. category: email



When receiving many mails a day, having a single mail pool makes easy to loose
important emails. Using mailboxes (ie: sub folders) with rules becomes a need.
The good point is all the mailboxes are regularly updated to alert when
incoming email are received.  The few steps are generating a mailbox tree,
mooving mails manually, mooving them automatically, and finally mooving the
history in batch. 

Generating a mailbox tree
-------------------------
Mailboxes consists of folder{cur, new, old} where *folder* represents its name.
The easy way to build them is from the mutt interface, by pressing `s` on an
email and choosing the new mailboxe name. The root of the mailboxe is defined
as the `folder` variable.


Mooving mails manually
----------------------
It is possible to redirect an email into a mailboxe by pressing `s` and then
`=myMailBoxeChoice`. In case you want to put it back to the general spool just
type `=` with an empty mailboxe.


Mooving mails automatically
---------------------------
I have been able to write rules based on email features such `from` with the
`helpful tutorial <https://userpages.umbc.edu/~ian/procmail.html#tutorial>`_.
In my setup, *getmail* fetches the mails and invokes *procmail* to redirect the
mail somewhere. The idea is to parse the email address with a regex and
depending on the result redirect it in the right `new` mailboxe folder.


Mooving mails history
---------------------

I haven't yet a solution for that.
