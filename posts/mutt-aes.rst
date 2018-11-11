.. title: Encrypt & Sign Mails
.. slug: mutt-aes
.. date: Aug 25, 2018
.. link: 
.. description:
.. tags: mutt, email
.. category: tools

Cacert.org offers validated certificates with a network of trusted people. That's fun !

.. END_TEASER

Get a certificate (eg: CAcert.org)
----------------------------------

- Client Certificate > new
- add it to Firefox

The key pair can then be extracted in p12 format from Firefox and it is
possible to transform it to rsa format:

.. code:: bash

        openssl pkcs12 -in ~/Downloads/cacert.p12 -out /home/$USER/.ssh/id_rsa_cacert


Installing neomutt from source on Ubuntu 18.04
----------------------------------------------

.. code:: bash

        sudo apt install libncursesw5-dev
        sudo apt install libidn11-dev
        git clone https://github.com/neomutt/neomutt
        cd neomutt
        ./configure
        make
        sudo make install

Loading the key into mutt
-------------------------

It is necessary to setup and use smime_key command to manage keys. It will
generate a KeyID for the certificate (something like xxxxxx.0)

.. code:: bash

        cp /home/$USER/git/neomutt/contrib/smime.rc ~/.smime.rc
        /usr/libexec/neomutt/smime_keys init
        mkdir -p ~/.smime/certificates
        touch ~/.smime/certificates/.index
        mkdir -p ~/.smime/keys
        touch ~/.smime/keys/.index
        /usr/libexec/neomutt/smime_keys add_p12 ~/Download/cacert.p12
        /usr/libexec/neomutt/smime_keys refresh


- Add to `source /home/$USER/.smime.rc` to .muttrc 
- Setup the KeyID in the *.smime.rc* file

Send a signed mail
---------------------

After having written the mail in neomutt type `shift-S` and choose the `s` to
sign the mail. It will choose the KeyID by default, and ask for the key
password if exists.

Loading a public key
--------------------

Right now, I don't know

Encrypt a mail
---------------

Choose `e` for encryption, and then `enter` to list the existing public keys.
