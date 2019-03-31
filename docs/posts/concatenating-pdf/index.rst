.. title: Concatenating pdf
.. slug: concatenating-pdf
.. date: 2018-12-17 14:48:34 UTC+01:00
.. tags: pdf
.. category: 
.. link: 
.. description: 
.. type: text

The command below is great to concat multiple pdf together. I had some problem with pdftk.

.. code-block:: bash

    gs -dBATCH -dNOPAUSE -q -sDEVICE=pdfwrite -sOutputFile=merged.pdf mine1.pdf mine2.pdf

Also this command allows to compress a large pdf into something smaller:

.. code-block:: bash

    gs -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 -dPDFSETTINGS=/default -dNOPAUSE -dQUIET -dBATCH -dDetectDuplicateImages -dCompressFonts=true -r150 -sOutputFile=output.pdf input.pdf

It is also possible to split pdf with pdfbox:

.. code-block:: bash

   java -jar /app/edsr/script/java/pdfbox-app-2.0.0-SNAPSHOT.jar PDFSplit ./authorisation-inscription.pdf 
