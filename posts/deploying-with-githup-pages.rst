.. title: Deploying Nikola with Githup Pages
.. slug: deploying-with-githup-pages
.. date: 2018-11-11 18:07:33 UTC+01:00
.. link: 
.. description: 
.. type: text
.. tags:  nikola, python
.. category: website development

The simple way
==============

Its about using the *docs* github pages method.

#. create a new github repository
#. create a */docs/* folder with the website in it
#. enable github pages and specify the *docs* folder as html source

Why the simple way
==================

The alternate way is to use a *gh-pages* branch to store the website. The main problem is the method leads to highly complex deployment method with `git subtree <http://www.damian.oquanta.info/posts/one-line-deployment-of-your-site-to-gh-pages.html>`_ or `other way <https://www.asmeurer.com/blog/posts/moving-to-github-pages-with-nikola/>`_

A second problem is this method make either duplicate website source code, or makes building the website for each post.
