Desalsim Tutorial
========

1. Introduction
------------
This tutorial provides a comprehensive overview and guide to utilizing a simulation package tailored for analyzing desalination and brine treatment technologies. Here's what you'll find:

2. **Usage:** Instructions on how to use the simulation models, including input parameters and result interpretation.
3. **Technical Process Models:** Detailed descriptions of each technology model, including input-output relationships and simulation steps.
4. **Economic Models:** Explanation of economic models for evaluating operating and investment costs.
5. **Treatment Chains Comparison:** Guidance on comparing different treatment chains using provided tools.

2. Installation
------------
The easiest way is through pip, in command-line interface:

.. code-block:: shell

   pip install desalsim

If you want to install the latest GitHub version:

- Download the repository to your local machine:

.. code-block:: shell

   https://github.com/rodoulak/Desalination-and-Brine-Treatment-Simulation-

- Install the required dependencies:

.. code-block:: shell

   pip install -r requirements.txt

3. Usage
-----
.. raw:: html

   <div style="text-align: justify;">
Each simulation model serves as a standalone tool for analyzing the performance of a specific desalination or brine treatment technology. Before running the simulation, ensure that you have provided the necessary input parameters, such as feed flow rates, salinity levels, membrane properties, heat sources, and operating conditions.

The simulation results, including salt concentration profiles, ion fluxes, energy consumption, chemical consumption, and operational costs, will be generated based on the specified inputs and displayed in the console output or saved to output files for further analysis.

However, simulation models of more than one technology can be combined to simulate and evaluate the performance of a treatment chain (desalination and brine treatment system). In this case, the output flow rates and stream concentrate are the input data for the next technology.

Additionally, two example files are provided to demonstrate the usage of the simulation suite (see `Example 1 <https://github.com/rodoulak/Desalination-and-Brine-Treatment-Simulation-/tree/main/example/example_1.py.py>`_ and `Example 2 <https://github.com/rodoulak/Desalination-and-Brine-Treatment-Simulation-/tree/main/example/example_2.py>`_). 
These examples simulate and evaluate two different treatment chains, showcasing the integration of multiple technologies. The economic evaluation of the treatment chain is given in `Example 1 <example_1.py>`_ and in the :doc:`Economic Tutorial </Tutorials/Economic_Tutorial>`.
Furthermore, a `comparison file <https://github.com/rodoulak/Desalination-and-Brine-Treatment-Simulation-/tree/main/example/comparison.py>`_ is included, where the results of the two examples are compared in terms of various parameters. Users can extend this comparison by adding more indicators as needed.

**Followed steps:**

Step 1: Import required functions for process units in the treatment chain.

Step 2: Set input data like feed flow rate, ion concentration, relevant ions for the feed solution.

Step 3: Set input parameters for each process unit as shown in **Table 1** and for economic model as shown in **Table 2** and **Table 3**.

Step 4: Call function of each process unit, create objects for each calculation.

Step 5: Results interpretation.

3.1. Documentation
-------------
You can find Tutorials and documents at:

- Tutorial for :doc:`Example 1 </Tutorials/Example_1_Tutorial>`
- :doc:`Economic Tutorial </Tutorials/Economic_Tutorial>`
- The mathematical description of each technology is given in `Mathematical description <https://github.com/rodoulak/Desalination-and-Brine-Treatment-Simulation-/tree/main/paper/Mathematical_description.pdf>`_
- `Example 1 <https://github.com/rodoulak/Desalination-and-Brine-Treatment-Simulation-/tree/main/example/example_1.py>`_
- `Example 2 <https://github.com/rodoulak/Desalination-and-Brine-Treatment-Simulation-/tree/main/example/example_2.py>`_
- `Scenarios comparison <https://github.com/rodoulak/Desalination-and-Brine-Treatment-Simulation-/tree/main/example/comparison.py>`_

4. Technical process models
------------------------
For more detailed steps and instructions see Tutorial for :doc:`Example 1 </Tutorials/Example_1_Tutorial>`.
The mathematical description of each technology is given in `Mathematical description <https://github.com/rodoulak/desalsim/blob/main/paper/Mathematical_description.pdf>`_.
**Table 1** gives an overview of the main inputs and outputs for each process unit in **Desalsim**.

.. list-table:: Table 1. Overview of Inputs and Outputs for each process unit in **Desalsim**. 
   :header-rows: 1
   :widths: 30 30 40

   * - Process
     - Input
     - Output
   * - Nanofiltration
     - Feed flow rate [m³/h]
     - Permeate flow rate and composition [g/L]
   * -
     - Ion concentration [g/L]
     - Concentrate flow rate and composition [g/L]
   * -
     - Osmotic pressure [bar]
     - Electrical requirements [kWhel]
   * -
     - Water recovery [%]
     -
   * -
     - Ion rejection [-]
     -
   * - Multi-effect distillation
     - Feed flow rate [m³/h]
     - Flow rate of water [m³/h]
   * -
     - Ion concentration [g/L]
     - Effluent flow rate and composition [g/L]
   * -
     - Feed temperature [°C]
     - Electrical [kWhel] and thermal [kWhth] requirements
   * -
     - Steam temperature [°C]
     - Cooling water flow rate [m³/h]
   * - Thermal crystallizer
     - Feed flow rate [m³/h]
     - Flow rate of water [kg/h]
   * -
     - Ion concentration [g/L]
     - Flow rate of NaCl [kg/h]
   * -
     - Feed temperature [°C]
     - Cooling water flow rate [m³/h]
   * -
     - Steam temperature [°C]
     - Electrical [kWhel] and thermal [kWhth] requirements
   * - Multi-plug flow reactor
     - Feed flow rate [m³/h]
     - Alkaline solution flow rate [L/h]
   * -
     - Ion concentration [g/L]
     - Flow rate of Mg(OH)₂ [kg/h]
   * -
     - Concentration of the alkaline solution (NaOH) [M]
     - Flow rate of Ca(OH)₂ [kg/h]
   * -
     - Concentration of the acid solution (HCl) [M]
     - Acid solution flow rate [L/h]
   * -
     -
     - Effluent flow rate [m³/h] and composition [g/L]
   * -
     -
     - Electricity requirements [kWhel]
   * - Eutectic freeze crystallizer
     - Feed flow rate [m³/h]
     - Flow rate of Na2SO4 [kg/h]
   * -
     - Ion concentration [g/L]
     - Flow rate of ice [kg/h]
   * -
     - Feed temperature [°C]
     - Effluent flow rate [m³/h] and composition [g/L]
   * -
     -
     - Electricity requirements [kWhel]
   * - Electrodialysis with bipolar membranes
     - Feed flow rate [m³/h]
     - Flow rate of acid [m³/h] and composition [g/L]
   * -
     - Ion concentration [g/L]
     - Flow rate of base [m³/h] and composition [g/L]
   * -
     - Current Density [A/m2] 
     - Flow rate of salt [m³/h] and composition [g/L]
   * -
     -
     - Electricity requirements [kWhel]
   * - Electrodialysis
     - Feed flow rate [m³/h]
     - Flow rate of diluted stream [m³/h] and composition [g/L]
   * -
     - Ion concentration [g/L]
     - Flow rate of concentrate stream [m³/h] and composition [g/L]
   * -
     - Current Density [A/m2] 
     - Electricity requirements [kWhel]


5. Economic models 
------------

For more detailed steps and instructions see :doc:`Economic Tutorial </Tutorials/Economic_Tutorial>`.  
The mathematical description of economic model is given also in `Mathematical description <https://github.com/rodoulak/desalsim/blob/main/paper/Mathematical_description.pdf>`_.  

**Table 2** gives an overview of the main inputs and outputs of economic model (``economic_f.py``). 

.. list-table:: Table 2. Overview of Inputs and Outputs of economic models. 
   :header-rows: 1
   :widths: 50 50

   * - Input
     - Output
   * - Selling price for products [€/ton] or [€/m³]
     - Operating cost (OPEX) [€/year]
   * - Prices for energy [€/KWh], input chemicals [€/m³], cooling water [€/m³]
     - Investment cost (CAPEX) [€]
   * - Operating hours, lifetime
     - Revenues from selling products [€/year]
   * - Interest rate, Inflation rate
     -
   * - Equipment cost [€]
     -
   * - Assumptions on CAPEX and OPEX calculations
     -


For the economic analysis of a full-scale desalination plant, the equipment costs of pilot-scale units are scaled-up to a capacity of 30000 m³/d. The equipment (material) costs of the full-scale plant are derived from the cost of the same equipment in the pilot plant with known capacity using function ``scaleup.py``.

**Table 3** gives an overview of the main assumptions made to calculate the CAPEX and OPEX.

.. list-table:: Table 3. Overview of main assumptions for CAPEX and Annual OPEX calculations. 
   :header-rows: 1
   :widths: 50 50

   * - CAPEX
     - Annual OPEX
   * - Installation: 25% of purchased equipment cost
     - Maintenance: 3% of the fixed-capital investment
   * - Buildings, process, and auxiliary: 20% of purchased equipment cost
     - Operating Supplies: 5% of maintenance
   * - Land: 6% of purchased equipment cost
     - Operating Labor: 15% of annual OPEX
   * - Indirect costs: 15% of direct cost
     - Direct supervisory and clerical labor: 15% of operating labor
   * - Working capital: 20% of total investment cost
     - Laboratory charges: 15% of operating labor
   * - 
     - Patents and royalties: 3% of annual OPEX
   * - 
     - Fixed charges: 5% of annual OPEX
   * - 
     - Plant overhead costs: 5% of annual OPEX


6. Treatment chains comparison 
------------

In comparison file, results from different treatment chains are summarised. Indicators are formulated to compare the treatment chains. 

**Import results**

First, import the results from the two examples.

.. code-block:: python

    # Import results 
    import example_1 as sc1
    import example_2 as sc2

Import required functions: 

.. code-block:: python

    import numpy as np
    import pandas as pd
    import numpy as np 

**Create lists with results**

.. code-block:: python

    X = ['Example 1', 'Example 2']
    X_axis = np.arange(len(X))


**Electrical consumption Vs Thermal consumption**

For instance, the two examples are compared based on their electrical and thermal energy requirements. 

.. code-block:: python

    # Create lists for OPEX and assigned results for Electrical consumption and thermal consumption 
    Eel = [ sc1.sum_el_en, sc2.sum_el_en]
    Eth = [sc1.sum_th_en,   sc2.sum_th_en]
    # Yearly calculation 
    Eel = [i * hr/1e6 for i in Eel] # Total electrical energy consumption 
    Eth = [i * hr/1e6 for i in Eth] # Total thermal energy consumption 

*Visualization*

For the visualization, a bar figure is created and saved.  

.. code-block:: python

    # Create Figure 1: Electrical consumption Vs thermal consumption 
    plt.bar(X_axis - 0.2, Eel, 0.4, color="#00516a", label = 'Electrical (GWel)')
    plt.bar(X_axis + 0.2, Eth, 0.4, color="sandybrown", label = 'Thermal (GWth)')
    plt.xticks(X_axis, X)
    plt.ticklabel_format(style='sci', axis='y', scilimits=(0,0))
    plt.xlabel("Scenarios")
    plt.ylabel("Energy consumption (GW)")
    plt.legend()
    plt.savefig('electricVSthermal.png')
    plt.show()

.. figure:: https://github.com/rodoulak/Desalination-and-Brine-Treatment-Simulation-/assets/150446818/640b2dde-d5c6-439d-a44d-fc0e73dcf342
   :width: 600px
   :alt: Image


**Operating costs (OPEX)**

For instance, the two examples are compared based on the operating costs (OPEX). 

.. code-block:: python

  # Create lists for OPEX and asigned reuslts 
  OPEX = [sc1.OPEX, sc2.OPEX]
  # Yearly calculation 
  OPEX = [i/1e6 for i in OPEX]

*Visualization* 

For the visualization, a bar figure is created and saved.  

.. code-block:: python
  # Create Figure 2: OPEX 

  plt.bar(X_axis - 0.4, OPEX, 0.4, color="#00516a")    
  plt.xticks(X_axis, X)
  plt.xlabel("Scenarios")
  plt.ylabel("OPEX (M€/year)")
  plt.savefig('OPEX.png')
  plt.show()

.. figure:: https://github.com/rodoulak/Desalination-and-Brine-Treatment-Simulation-/assets/150446818/71c0fa99-49f8-4b69-916a-48cdaa6d303e
   :width: 600px
   :alt: Image


