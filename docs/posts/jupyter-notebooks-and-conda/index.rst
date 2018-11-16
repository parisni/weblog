.. title: Jupyter Notebooks and Conda
.. slug: jupyter-notebooks-and-conda
.. date: 2018-11-16 19:11:17 UTC+01:00
.. tags:  python, jupyter
.. category:  data science
.. link: 
.. description: 
.. type: text


After creating a environment with conda and installing packages, the jupyter
notebook environment may not see them. The method below adds a new kernel based
on the later environment and allow to benefit from conda:

.. code-block:: bash
   
   conda activate my-env
   conda  create -n my-env pip setuptools ipykernel python=3.5
   python -m ipykernel install --user --name my-env --display-name "Python (my-env)"
