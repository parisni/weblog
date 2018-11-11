.. title: Postgresql 11 on Archlinux
.. slug: archlinux-pgsql-11
.. date: Oct 27, 2018
.. link: 
.. description:
.. tags: postgresql, archlinux
.. category: system administration




I am fairly new to archlinux. Postgresql 11 is fairly new too. Here are the steps to install that version.

.. END_TEASER


.. code-block:: bash

      # get the lattest postgres in the repos
      # avoid checking for sha fingerprint, because not the same version
      yaourt --m-arg "--skipchecksums --skippgpcheck" -Sb postgresql
      # 1. edit the PKGBUILD file, change to 11.0 version
      # 2. comment the check() part
      # 3. after building and installing, change to 11 the PGMAJORVERSION within:
      sudo vim /usr/bin/postgresql-check-db-dir 
      # make the link for psql command working
      sudo ln -s /usr/lib/libreadline.so.7 /usr/lib/libreadline.so.6
      # install that old version to make posgres command work
      yaourt -S icu54 

That's all. By the way, I do not have any idea right now on how to upgrade minor versions in the future, but I will employ myself to update this post.
