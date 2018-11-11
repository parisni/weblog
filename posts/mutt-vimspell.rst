.. title: Spelling, mutt & vim
.. slug: mutt-vim
.. date: Sep 03, 2018
.. tags: mutt, vim
.. author: Nicolas Paris
.. link: 
.. description:
.. category: email



Having a correct spelling is important in society. This is true specially when
come time to write emails.

.. END_TEASER

Here is a trick to activate vim spelling while editing email in **mutt**. Add
the following line into your .muttrc configuration file:

.. code:: bash

        set editor="vim -c 'set textwidth=72' -c':set spell' -c':setlocal spell spelllang=en_us'"
 
This allows to check English spelling correctly.

Enjoy !
