.. title: A decent python configuration
.. slug: a-decent-python-configuration
.. date: 2018-11-11 11:43:34 UTC+01:00
.. link: 
.. description: 
.. type: text
.. tags: python
.. category: data science

Use Anaconda
============

Anaconda allows to have multiple python versions. This is much easier than using *virtualenv*

.. END_TEASER

.. code-block:: bash

   conda env list # lists all envs
   conda env create py3.5 pip setuptools python=3.5 # create a python env
   conda activate py3.5 # activate the latter
   conda deactivate # deactivate it
   conda env remove -n py3.5 # remove it completely


Use pip together with anaconda
==============================

The package will be installed within the environement. pip has a much broader
list of packages than the anaconda channel.

.. code-block:: bash

   pip install <my-package> # install a package
   pip install --index-url https://pypi.org/simple/ <my-package> # specify the repository
   pip install . # a local package
   pip remove <my-package>  # remove it
