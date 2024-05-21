.. DesalSim: Desalination and Brine Treatment simulation model documentation master file, created by
   sphinx-quickstart on Fri May 17 16:03:22 2024.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to DesalSim: Desalination and Brine Treatment simulation model's documentation!
=======================================================================================
.. raw:: html

   <div style="text-align: justify;">
This project comprises a package of simulation models for desalination and brine treatment technologies, including reverse osmosis, nanofiltration, multi-effect distillation, chemical precipitation, eutectic freeze crystallization, electrodialysis, and thermal crystallization. These technologies are utilized for desalination, ion separation, and salt recovery processes in various industrial and environmental applications.

The simulation models implemented here calculate various parameters such as salt concentration profiles, ion fluxes, energy consumption, chemical consumption, and operational costs. They provide insights into the performance and economics of the technologies under different operating conditions and input parameters. Additionally, the models allow for the integration of different technologies in various configurations to optimize process efficiency and resource utilization.

.. figure:: https://github.com/rodoulak/Desalination-and-Brine-Treatment-Simulation-/assets/150446818/bb10e07d-b878-45c8-878a-0c56222546cf
   :width: 600px
   :alt: Image


Installation
------------
The easiest way is through pip, in command-line interface:

.. code-block:: shell

   pip install DesalSim

If you want to install the latest GitHub version:

- Download the repository to your local machine:

.. code-block:: shell

   https://github.com/rodoulak/Desalination-and-Brine-Treatment-Simulation-

- Install the required dependencies:

.. code-block:: shell

   pip install -r requirements.txt


.. toctree::
   :maxdepth: 2
   :hidden:
   :caption: Tutorials:

   Tutorials/Desalsim_Tutorial
   Tutorials/Economic_Tutorial
   Tutorials/Example_1_Tutorial
   Tutorials/EDBM_Tutorial
   Tutorials/MED_Tutorial
   Tutorials/NF_Tutorial
   Tutorials/MF-PFR_Tutorial
   Tutorials/TCr_Tutorial

.. toctree::
   :maxdepth: 2
   :hidden:
   :caption: File description:

   File descr/Description 

.. toctree::
   :maxdepth: 2
   :hidden:
   :caption: Content:

   Contributors
