.. title: Mutt Search on Steroids
.. slug: mutt-search
.. date: Aug 30, 2018
.. tags: mutt, mu
.. author: Nicolas Paris
.. link: 
.. description:
.. category: email



Mutt has a powerful feature to dig into the history: the **limit** feature. By
pressing `l` it is then possible to write a query to filter the mails. It is
comparable to the **WHERE** part of a SQL statement and allow combining **AND**
operators. In particular the **~b** predicate allow to search within the email's
body and the `~X` lets filter attachments.

.. END_TEASER

However that feature acts as a **grep** command and parse all the mails one by
one. Searching in multiple dozen of thousand emails can last dozen of minutes.
Here comes the **notmuch** program, that improves greatly the limit feature with
instant search and some other goodies (and also drawbacks). The secret of this
speed is it scan emails and produces a full text index to be used later.

The installation is straightforward on ubuntu with `sudo apt install notmuch`.
The configuration is also piece of cake: `notmuch config` will setup the
maildir folder. Later the `notmuch new` command allows to the mails and can be
run again when new mails arrive. There is also a way to automatize the
indexation by setup system notifications `right there <https://github.com/noah/notmuch-new-inotify>`_.

The integration in mutt to replace the *limit* feature only need the two rows
below in the *.muttrc* configuration file. Pressing `L` will propose to write a
query to filter the mails based on the index `more information here <http://log.or.cz/?p=228>`_. 
To get the full details on how to search within notmuch: `man
notmuch-search-index` describes the syntax of the search.

.. code-block:: bash

        # 'L' performs a notmuch query, showing only the results
        macro index L "<enter-command>unset wait_key<enter><shell-escape>read -p 'notmuch query: ' x; echo \$x >~/.cache/mutt_terms<enter><limit>~i \"\`notmuch search --output=messages \$(cat ~/.cache/mutt_terms) | head -n 600 | perl -le '@a=<>;chomp@a;s/\^id:// for@a;s/\\+/\\\\+/g for@a;s/=/\\\\=/g for@a;$,=\"|\";print@a'\`\"<enter>" "show only messages matching a notmuch pattern"
        # 'a' shows all messages again (supersedes default <alias> binding)
        macro index a "<limit>all\n" "show all messages (undo limit)"

There is some caveats with notmuch however. Its search within attachment is not
as powerfull as the built-in mutt. In particular, the later allows to specify
the number of attachments. Last but not least, notmuch does only provide a
stemmer for english. That's might be possible to extend it with an existing
implementation of the french snowball parser.

EDIT:
-----

**mu** is an alternative to **notmuch** . It covers the features described above, and has a better syntax.

Here is the full documentation there `mu documentation <http://manpages.ubuntu.com/manpages/bionic/man1/mu-find.1.html>`_

.. code-block:: bash

        # Install
        sudo apt install maildir-utils

        # Index Maildir
        mu index -m my/maildir
        
        # setup in crontab or use inotify service

        # to add to .muttrc
        # Mode 1.
        #
        # this allows to search into all mailbowes at once
        # however, it is a readonly mailbox because off symlinks
        macro index <F8> "<shell-escape>mu find --clearlinks --format=links --linksdir=~/Maildir/search "   "mu find"
        macro index <F9> "<change-folder-readonly>~/Maildir/search"      "mu find results"

        # Mode 2.
        #
        # this allows to show the 600 first emails
        # it is 
        macro index L "<enter-command>unset wait_key<enter><shell-escape>read -p 'mu query: ' x; echo \$x >~/.cache/mutt_terms<enter><limit>~i \"\`mu find --fields i --quiet 2> /dev/null \$(cat ~/.cache/mutt_terms) | head -n 600 | perl -le '@a=<>;chomp@a;s/\^id:// for@a;s/\\+/\\\\+/g for@a;s/=/\\\\=/g for@a;$,=\"|\";print@a'\`\"<enter>" "show only messages matching a mu pattern"
        # 'a' shows all messages again (supersedes default <alias> binding)
        macro index a "<limit>all\n" "show all messages"

