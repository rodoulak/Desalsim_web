Tutorial: Electrodialysis with Bipolar membranes (EDBM) unit
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

EDBM is a membrane-based technology that allows the production of acidic and alkaline solutions by applying an electric potential to the electrodes. In this work, EDBM aims to convert NaCl molecules of a brine solution to NaOH and HCl solutions.

.. image:: https://github.com/rodoulak/Desalination-and-Brine-Treatment-Simulation-/assets/150446818/2580e05b-73f2-4fa4-8d52-71762088fc17
   :alt: edbm

In **desalsim** package, the EDBM unit is used to model the operation of a Electrodialysis with Bipolar membranes technology. Upon simulation, it calculates the flow rate of the acid, base, and salt solutions, their ion concentration, and the electricity requirements of the unit. The EDBM function consists of one class: `EDBMCalc`_ that consists of various methods.  
In this tutorial, we will focus on how to use the class and their methods.

**Table 1: Overview of Inputs and Outputs for Electrodialysis with Bipolar Membranes**

+-----------------------------------------------+---------------------------------------------+-------------------------------------------------------+
| Process                                       | Input                                       | Output                                                |
+===============================================+=============================================+=======================================================+
|                                               | Feed flow rate [m3/h]                       | Flow rate of acid [m3/h] and composition [g/L]        |
+-----------------------------------------------+---------------------------------------------+-------------------------------------------------------+
|                                               | Ion concentration [g/L]                     | Flow rate of base [m3/h] and composition [g/L]        |
+-----------------------------------------------+---------------------------------------------+-------------------------------------------------------+
|                                               | Electric density                            | Flow rate of salt [m3/h] and composition [g/L]        |
+-----------------------------------------------+---------------------------------------------+-------------------------------------------------------+
|                                               |                                             | Electricity requirements [kWhel]                      |
+-----------------------------------------------+---------------------------------------------+-------------------------------------------------------+

The mathematical description of Electrodialysis with Bipolar membranes technology is given in `Mathematical description`_, see Section A.6.

.. _Mathematical description: https://github.com/rodoulak/Desalination-and-Brine-Treatment-Simulation-/tree/main/paper/Mathematical_description.pdf

1. Getting started
==================

1.1. Import class
-----------------

.. code-block:: python

   import desalsim

Then import the class:

.. code-block:: python

   from desalsim.edbm_unit_f import EDBMCalc

Additionally, functions for calculating density (`density_calc.py`) or constants (`comparison.py`) where the user can add constant values like MW, prices etc, need to be imported.

.. code-block:: python

   from desalsim.density_calc import density_calc
   from desalsim import constants
   from desalsim import scaleup

1.2. Define feed characteristics
--------------------------------

You can initialize the feed solution by setting the flow rate, specifying the focus components and their concentration.

.. code-block:: python

   # Feed concentration
   components = ['Na', 'Cl', 'K', 'Mg', 'Ca', 'SO4', 'HCO3', 'H', 'OH']
   Cin_edbm = [13.44, 20.725, 1.146, 0, 0, 0.18, 0, 10**(-ph_s), 3.01551E-11]

   # Feed flow rate L/h
   Q_in_edbm = 47000

   # Input conditions
   ph_s = 4.71  # pH salt channel (units: -)
   ph_b = 7  # pH base channel (units: -)
   ph_a = 7  # pH acid channel (units: -)

   # Feed concentration for base and acid channels
   C_b_in = [0, 0, 0, 0, 0, 0, 0, 10**(-ph_b), 10**(-(14-ph_b))]
   C_a_in = [0, 0, 0, 0, 0, 0, 0, 10**(-ph_a), 10**(-(14-ph_a))]

   # Temperature
   T = 20 + 273.15  # K

.. note::

   Note that if you want to add more components, you need to update the components list and include the concentration of the new component in the *Cin_edbm*.

You can calculate the density of the feed solution and the water quantity in inflow:

.. code-block:: python

   d_in = density_calc(25, sum(Cin_edbm)) / 1000
   d_s = d_in

   # Calculate water quantity in inflow
   Mw_in = Q_in_edbm / d_in

1.3. Set operating assumptions
------------------------------

You need to set operating assumptions such as the electrical current density.

.. code-block:: python

   # Assumptions:
   # The electrical current density
   I_d = 400  # Am2
   # Set number of triplets
   N_trip = 50 * 47  # Number of triplets based on the inlet flow rate
   # Set membrane area based on the feed flow rate, m2
   A = 0.4  # range: 0.1-1

Finally, you need to set assumptions related to pumping like pressure drop (*dp*) and pump efficiency (*npump*).

.. code-block:: python

   npump = 0.8  # pump efficiency (units: -)
   dp = 1  # pressure drop (units: bar)


1.4. Set Mmebrane characteristics 
----------------------------

You need to set Membrane characteristics:

.. code-block:: python

   # Membrane characteristics
   Cm_bp_H = 0.0000001  # mol/l
   Cm_bp_OH = 0.0000001  # mol/l

1.5. Set constants 
-------------

You need to set constant parameters:

.. code-block:: python

   F = 96485.3  # Coulombs/mol
   R_const = 8.314462618  # kg⋅m2⋅s−2⋅K−1⋅mol−1
   # R_int = 0.28  # ohm cm2
   R_int = 45  # ohm cm2
   z = 1

After setting all the required inputs, then you can create the functions' objectives.

2. Use EDBMCalc class   
==================

EDBMCalc is a class used to represent mass and energy balance for EDBM Unit. In particular, it calculates the flowrate in each channel, the outlet concentration in each channel, the external Voltage and power needed.
EDBMCalc takes as input the feed flow rate (*Qin*), the membrane area (*A*), the electrical current density (*I_d*), the Number of triplets based on the inlet flow rate (*N*), the initial concentrations of various ions in the salt channel (*Ci_s_in*), base channel (*Ci_b_in*), and acid channel (*Ci_a_in*) and the feed temperature (*T*).

2.1. Overview
--------

The following attributes are available within the EDBMCalc class:

- ``CNa_in``, ``CCl_in``, ``CK_in``, ``CMg_in``, ``CCa_in``, ``CSO4_in``: Initial concentrations of various ions (g/l).
- ``CNa_out``, ``CCl_out``, ``CK_out``, ``CMg_out``, ``CCa_out``, ``CSO4_out``: Outlet concentrations of various ions (g/l).
- ``Ci_s_in``: Initial concentrations of various ions in the salt channel (mol/l).
- ``Ci_a_in``: Initial concentrations of various ions in the acid channel (mol/l).
- ``Ci_b_in``: Initial concentrations of various ions in the base channel (mol/l).
- ``EMF``: Electromotive force (V).
- ``KW_s_in``: Inlet ionic water product in the salt channel.
- ``KW_a_in``: Inlet ionic water product in the acid channel.
- ``KW_b_in``: Inlet ionic water product in the base channel.
- ``M_h2o_a_in``: Initial mass flow rate of water in the acid channel (kg/h).
- ``M_h2o_b_in``: Initial mass flow rate of water in the base channel (kg/h).
- ``M_h2o_s_in``: Initial mass flow rate of water in the salt channel (kg/h).
- ``N_trip``: Number of triplets of a channel.
- ``P``: Gross power needed (W).
- ``PM``: Molecular weight.
- ``Q``: Flow rate (l/h).
- ``V_ext``: Voltage needed (V).

The EDBMCalc class provides the following methods:

.. code-block:: python

   # Calculates the flowrate in each channel 
   flowrate()
   # Calculates the inlet mass flow rates of each ion, kg/h
   in_mass_flow_rates(ph_s)
   # Performs mass balance calculations for Acid channel 
   acid_channel()
   # Performs mass balance calculations for Base channel
   base_channel()
   # Performs mass balance calculations for Salt channel 
   salt_channel(Cm_bp_H, Cm_bp_OH)

2.2. Create EDBMCalc objects
-----------------------

EDBMCalc takes as input the feed flow rate (*Qin*), the membrane area (*A*), the electrical current density (*I_d*), the Number of triplets based on the inlet flow rate (*N*),
the initial concentrations of various ions in the salt channel (*Ci_s_in*), base channel (*Ci_b_in*), and acid channel (*Ci_a_in*) and and the feed temperature (*T*).  
 
Create an instance of the EDBMCalc class with the defined parameters:

.. code-block:: python

   # Create an instance of the EDBMCalc class with the defined parameters
   edbm_dat = EDBMCalc(Q_in_edbm, A, I_d, N_trip, Cin_edbm, C_b_in, C_a_in, T)

2.3. Use ``flowrate`` method
---------------------

This method calculates the flowrate in each channel (``Q1_s_in``, ``Q1_a_in``, ``Q1_b_in``).

.. code-block:: python

   flowrate()

It doesn't take additional inputs.

2.4. Use ``in_mass_flow_rates`` method
-------------------------------

This method calculates the inlet mass flow rates. In particular, it calculates the inlet mass flow rates of each ion in the three channels (``M_s_in``, ``M_a_in``, ``M_b_in``), the mass of water in the initial streams in the three channels (``M_h2o_s_in``, ``M_h2o_a_in``, ``M_h2o_b_in``). Additionally, it calculates the inlet ionic water product in each channel (``KW_s_in``, ``KW_a_in``, ``KW_b_in``).

.. code-block:: python

   in_mass_flow_rates(ph_s)

It takes the initial pH in the salt channel as input. The results are used in the following calculations.

2.5. Use ``acid_channel`` method
-------------------------

It calculates the mass balance calculations for Acid channel. In particular, it calculates the outlet mass flow rate for all ionic species in the channel (``M_a_out``) and water (``M_h2o_a_out``), the total outlet mass flow rate (``M_a_out_t``), volumetric outlet flow rate (``Q1_a_out``), and the outlet concentration of single ions in the channel (``Ci_a_out``).

.. code-block:: python

   acid_channel()

It doesn't take additional inputs.

2.5.1. Assigned the results to output parameters
-----------------------------------------

You can assign the results to output parameters:

.. code-block:: python

   Ca_out = edbm_dat.Ci_a_out
   Ca_out = edbm_dat.Ci_a_out[0:6]
   Ca_out_g = [Ca_out[0] * MW_Na, Ca_out[1] * MW_Cl, Ca_out[2] * MW_K, Ca_out[3] * MW_Mg, Ca_out[4] * MW_Ca, Ca_out[5] * MW_SO4]

   # Mass flow rate
   M_a_out = edbm_dat.M_a_out_t * N_trip

   # Volumetric flow rate
   Q_a_out = edbm_dat.Q1_a_out * N_trip

   # Conversion to solid
   M_HCl_out = Q_a_out * constants.MW_HCl / 1000  # kg/hr

2.6. Use ``base_channel`` method
-------------------------

It calculates the mass balance calculations for Base channel. In particular, it calculates the outlet mass flow rate for all ionic species in the channel (``M_b_out``) and water (``M_h2o_b_out``), the total outlet mass flow rate (``M_b_out_t``), volumetric outlet flow rate (``Q1_b_out``), and the outlet concentration of single ions in the channel (``Ci_b_out``).

.. code-block:: python

   base_channel()

It doesn't take additional inputs.

2.6.1. Assigned the results to output parameters
-----------------------------------------

You can assign the results to output parameters:

.. code-block:: python

   # Base channel
   # Concentration in base channel
   Cb_out = edbm_dat.Ci_b_out[0:6]
   Cb_out_g = [Cb_out[0] * MW_Na, Cb_out[1] * MW_Cl, Cb_out[2] * MW_K, Cb_out[3] * MW_Mg, Cb_out[4] * MW_Ca, Cb_out[5] * MW_SO4]

   # Mass flow rate
   M_b_out = edbm_dat.M_b_out_t * N_trip

   # Volumetric flow rate
   Q_b_out = edbm_dat.Q1_b_out * N_trip

   # Conversion to solid
   M_NaOH_out = Q_b_out * edbm_dat.Ci_b_out[0] * constants.MW_NaOH / 1000  # kg/hr


2.7. Use ``salt_channel`` method
----------------------------------
It calculates the mass balance calculations for Salt channel and the Voltage (`V_ext`) and Power (`P`) needed. In particular, it calculates the outlet mass flow rate for all ionic species in channel (`M_s_out`) and water (`M_h2o_s_out`), the total outlet mass flow rate (`M_s_out_t`), volumetric outlet flow rate (`Q1_s_out`) and the outlet concentration of single ions in channel (`Ci_s_out`).

.. code-block:: python

    salt_channel(Cm_bp_H, Cm_bp_OH)

It takes additional inputs Cm_bp_H, Cm_bp_OH, membrane characteristics.

2.7.1. Assigned the results to output parameters
---------------------------------------------------
You can assigned the results to output parameters:

.. code-block:: python

    "Salt channel "
        # Concentration in salt channel
        Cbrine_out_t = sum(edbm_dat.Ci_s_out)
        Cbrine_out = edbm_dat.Ci_s_out  # mol/l
        Cbrine_out_g = [
            Cbrine_out[0] * MW_Na, Cbrine_out[1] * MW_Cl, Cbrine_out[2] * MW_K,
            Cbrine_out[3] * MW_Mg, Cbrine_out[4] * MW_Ca, Cbrine_out[5] * MW_SO4
        ]  # g/l

        # Mass flow rate
        M_s_out = edbm_dat.M_s_out_t * N_trip

        # Volumetric flow rate
        Q_s_out = edbm_dat.Q1_s_out * N_trip

2.8. Calculate energy consumption
----------------------------------
You can calculate the total energy requirements for the EDBM unit. For this, you can use the voltage needed (`V_ext`) and the energy for pumping (`Ppump`).

.. code-block:: python

    # Energy consumption
    V_ext = edbm_dat.V_ext  # External

    # Calculate energy consumption for pumping
    Ppump = (edbm_dat.Q1_s_in * N_trip * dp + edbm_dat.Q1_a_in * N_trip * dp + edbm_dat.Q1_b_in * N_trip * dp) / 1000 / 3600 * 1e5 / npump  # units: W -> l to m3 so /1000; bar to J 1e5N/m2*1J/m ; hr to 3660s

    # Total energy consumption
    E_el_Edbm = V_ext * I_d * A / 1000 + Ppump / 1000

Additionally, the current efficiency (`CE`) can be calculated. In this work, ideal phenomena are assumed so it is expected to be close to 100%.

.. code-block:: python

    # Calculate current efficiency
    Cb_in = [0]
    CE = (Q_b_out) * (Cb_out[0] - Cb_in[0]) * F / (3600 * N_trip * I_d * A) * 100  # %

Finally, the specific energy consumption (kWh/kg of NaOH) can be calculated:

.. code-block:: python

    # Specific energy consumption (kWh/kg of NaOH)
    SEC = (V_ext * I_d * A) / (Q_b_out * (edbm_dat.Ci_b_out[0] - edbm_dat.Ci_b_in[0]) * constants.MW_NaOH)

2.9. Print results
-------------------
You can print results from the calculations:

.. code-block:: python

    "Salt channel "
    print("Salt channel: Mass flow rate out is " + str(round(M_s_out, 2)) + "kg/hr")
    print("Salt channel: Volumetric flow rate out is " + str(round(Q_s_out, 2)) + "l/hr")
    print("Na concentration:" + str(round(Cbrine_out[0], 2)) + "M and " + str(round(Cbrine_out_g[0], 2)) + "g/l")
    print("Cl concentration:" + str(round(Cbrine_out[1], 2)) + "M and " + str(round(Cbrine_out_g[1], 2)) + "g/l")
    print("-----------------------------------------")

Salt channel: Mass flow rate out is 74395.17kg/hr  
Salt channel: Volumetric flow rate out is 46487.68l/hr  
Na concentration:0.29M and 6.65g/l  
Cl concentration:0.29M and 10.26g/l  

.. code-block:: python

    "Base channel "
    print("Base channel: Mass flow rate out is " + str(round(M_b_out, 2)) + "kg/hr")
    print("Base channel: Volumetric flow rate out is " + str(round(Q_b_out, 2)) + "l/hr")
    print("Na concentration " +str(round(Cb_out[0],2))+"M and "+str(round(Cb_out_g[0],2))+"g/l")
    print("-----------------------------------------")

Base channel: Mass flow rate out is 35245.92kg/hr  
Base channel: Volumetric flow rate out is 34954.86l/hr  
Na concentration 0.4M and 9.23g/l  

.. code-block:: python

    "Acid channel" 
    print("Acid channel: Mass flow rate out is "+str(round(M_a_out,2))+"kg/hr")
    print("Acid channel: Volumetric flow rate out is "+str(round(Q_a_out,2))+"l/hr")
    print("Cl concentration "+str(round(Ca_out[1],2))+"M and "+str(round(Ca_out_g[1],2))+"g/l")
    print("-----------------------------------------")

Acid channel: Mass flow rate out is 34943.55kg/hr  
Acid channel: Volumetric flow rate out is 34943.55l/hr  
Cl concentration 0.4M and 14.23g/l  

.. code-block:: python

    # Energy consumption 
    print("Current efficiency is "+str(round(CE,2))+"%")
    print("-----------------------------------------")
    print("Total electrical consumption for EDBM is " + str(round(E_el_Edbm,2))+ " KW")
    print("Specific energy consumption is "+str(round(SEC,2))+"kwh/kg NaOH")

Current efficiency is 100.0%  

Total electrical consumption for EDBM is 942.6 KW  

Specific energy consumption is 1.67kwh/kg NaOH  

