Tutotrial: Multi-effect distillation (MED) unit
++++++++++++++++++

MED is a thermal based process that is used to desalinate water. In this work, MED aims to recover high quality water and concentrate further the brine stream.

.. figure:: https://github.com/rodoulak/Desalination-and-Brine-Treatment-Simulation-/assets/150446818/54c49681-d70f-4cf4-ab5f-4a5d7d8791b7
   :width: 600px
   :alt: Image

.. raw:: html

   <div style="text-align: justify;">
In **desalsim** package, the MED unit is used to model the operation of a Multi-effect distillation technology. Upon simulation, it will generate the influent/effluent mass flows and their concentrations, the cooling water, and the energy requirements.
The MED function consists of one class: `MEDCalculator <#use-medalculator-class>`_ that constsis.  
In this tutorial, we will focus on how to use the the class and their methods.  **Table 1** gives an overview of the main inputs and outputs for each process unit of Multi-effect distillation. 

.. list-table:: Table 1. Overview of Inputs and Outputs of Multi-effect distillation
   :header-rows: 1
   :widths: 33 33 34

   * - Process
     - Input
     - Output
   * - Multi-effect distillation
     - Feed flow rate [m3/h]
     - Flow rate of water [m3/h]
   * -
     - Ion concentration [g/L]
     - Effluent flow rate and composition [g/L]
   * -
     - Feed temperature [°C]
     - Electrical [kWhel] and thermal [kWhth] requirements
   * -
     - Steam temperature [°C]
     - Cooling water flow rate [m3/h]


The mathematical description of Multi-effect distillation technology is given in [Mathematical description](https://github.com/rodoulak/Desalination-and-Brine-Treatment-Simulation-/tree/main/paper/Mathematical_description.pdf), see Section A.2. 

1. Getting started 
========
1.1. Import class
-----
.. code-block:: python

    import desalsim

Then import the class:  

.. code-block:: python

    from desalsim.med_unit_f import MEDCalculator

Additionally, function for calculating density (``density_calc.py``) need to be imported. 

.. code-block:: python

    from desalsim.density_calc import density_calc

1.2. Define feed characteristics
-----
You can initialize the feed solution by setting the flow rate, specifying the focus components and their concentration. 

.. code-block:: python

    #Feed concentration
    components = ['Na', 'Cl', 'K', 'Mg', 'Ca', 'SO4']
    Cin_med = [10.36, 15.39, 0.36, 0.028, 0.02, 0.07]

    #Feed flow rate 
    Qf_med =1000 #l/hr

    #input conditions
    T=20

Note that if you want to add more components, you need to update the components list and include the concentration of the new component in the *Ciin_med*. 

You can calculate the density of the feed solution and the mass flow rate:

.. code-block:: python

    d=density_calc(T, sum(Cin_med))

    # Mass flow rate (units: kg/hr)
    Mf_med=Qf_med*d/1000 

1.3. Set operating assumptions  
-----
You need to set operating assumptions related to temperatures such as the temperature in the last effect, the intake/outake cooling water temperature etc.  

.. code-block:: python

    # Assumptions:
    T_in=40 #(oC)
    T_N=45 #Temperature in the last effect (oC)
    T_cw_in=25 #intake cooling water temperature (oC)
    T_cw_out=35 #out cooling water temperature (oC)
    T_s=70 #steam temperature oC
    DT_loss=1 #temperature difference (oC)
    T3=69

**Calculate latent heat of motive steam**

The latent heat of motive steam is calculated based on the set steam temperature and steam tables. 

.. code-block:: python

    #latent heat of motive steam:
    if T_s<=55:
        lh_s=2370
    elif T_s>55 and T_s<=60:
        lh_s=2358
    elif (T_s>60) and (T_s<=65):
        lh_s=2345
    elif (T_s>65) and (T_s<=70):
        lh_s=2333
    elif (T_s>70) and (T_s<=75):
        lh_s=2321


Additionally, you need to define the aimed brine vonventration leaving effect n (*Cb_out*) and the brine circulation flow rate (*Xr*), and the number of effects (*N*). 

.. code-block:: python

    Cb_out=200 #Brine Concentration leaving effect n (unit: g/l)
    Xr=5.5 # brine circulation flow rate (units: -)
    N=2 #Number of effects (-)

Finally, you need to set assumptions related to pumping like pressure drop (*dp*) and pump efficiency (*npump*). 

.. code-block:: python

    dp=0.1  # pressure drop (units: bar)
    dp_slurry=1 # pressure drop (units: bar)
    npump=0.8 #pump efficiency (units: -)

1.4. Set constants 
-----
You need to set constant parameters like the specific heat capacity of water:

.. code-block:: python

    Cp_w=4182 # specific heat capacity of water (j/kgC)
    cp_sol=4184 # specific heat capacity of solution (j/kgC)

After setting all the required inputs, then you can create the functions' objectives. 

.. _use-medalculator-class:

2. Use MEDCalculator class   
========
.. raw:: html

   <div style="text-align: justify;">
MEDCalculator is a class used to represent Mass Balance for MED Unit. In particular, it calculates the permeate and concentrate flow rates, and their ion concentrations. 
MEDCalculator takes as input the names of components (*comp*), the ion concentration in the feed (*C_in*), the rejection rates of the ions (*rjr_values*), the % of water recovery (*Wrec*) and the feed flow rate (*Qf*).  

2.1. Overview 
-----
The following attributes are available within the MEDCalculator class:  

- `Qf`: (float) Flow rate (m^3/s).
- `CNa_in, CCl_in, CK_in, CMg_in, CCa_in, CSO4_in` : (float) Initial concentrations of various ions (g/l).

The MEDCalculator class provides the following method:

.. code-block:: python

    calculate_perm()

This method calculates the permeate and concentrate flow rates, as well as their corresponding ion concentrations based on the provided attributes. It is automatically called upon initialization of the class instance.

2.2. Create MEDCalculator objects
-----
.. raw:: html

   <div style="text-align: justify;">
MEDCalculator takes as input the feed volumetric flow rate (*Qf_med*) and mass flow rate (*Mf_med*), the concentration in the feed for 
the components (*CNa_in, CCl_in, CK_in, CMg_in, CCa_in, CSO4_in*) and the Cin_med[0], Cin_med[1], Cin_med[2], Cin_med[3], Cin_med[4], 
Cin_med[5], and the inlet temperature (*T_in*). 
 
.. code-block:: python

    # Create an instance of the MEDCalculator class
    med_dat = MEDCalculator(Qf_med, Mf_med, Cin_med[0], Cin_med[1], Cin_med[2], Cin_med[3], Cin_med[4], Cin_med[5], T_in)


2.3. Use ``salinity_calc`` method 
-----
This method calculates the inflow salinity. 

.. code-block:: python

    med_dat.salinity_calc()

It doesn't take additional inputs. 

2.4. Use ``temperature_calc`` method 
-----
This method calculate temperature-related parameters and it takes as input the temperature difference (*DT_loss*), temperature in the last effect (*T_N*), and steam temperature (*T_s). 

.. code-block:: python

    med_dat.temperature_calc(DT_loss, T_N, T_s)

2.5. Use ``mass_balance_med`` method 
-----
This method performs mass balance calculations. In particular, it calculates the brine flow rate of leaving effect n (*Bn*, *Qb*), and the total distillate flow rate (*Mdist*, *Qdist*). 

.. code-block:: python

    med_dat.mass_balance_med(Cb_out)


**2.5.1. Assigned the results to output parameters**

After the mass calculation, you can assigned the results to output parameters: 

.. code-block:: python

    #Brine flow rate 
    Qout_med=med_dat.Qb

    #Distillate water flow rate 
    Qprod_med=med_dat.Qdist

    #Calculate circulation flow rate 
    Qr=Xr*Qf_med


2.6. Use ``performance_parameters`` method 
-----
.. raw:: html

   <div style="text-align: justify;">
It calculates the performance parameters *Gain Output Ratio (GOR)*, *Condenser thermal load (Qc)*, *Cooling water flow rate (Qw)*, *Performance ratio (PR)*, *Condenser thermal load (Qsen)*, *Total thermal load*. 
It takes as input the latent heat of motive steam (*lh_s*) and the intake cooling water temperature (*T_cw_in*). 

.. code-block:: python

    med_dat.performance_parameters(lh_s,  T_cw_in)


**2.6.1. Assigned the results to output parameters**

You can assigned the results to output parameters: 

.. code-block:: python

    # Calculate required cooling water 
    Qcw = med_dat.QCW * 3600 #units: kg/hr

    # Calculate thermal energy consumption
    E_th_med = med_dat.Q_Tot


Then the Energy consumption of the technology and the specific energy consumption can be calculated: 

.. code-block:: python

    # Calculate energy consumption
    # Electrical energy consumption
    E_el_med = ((Qf_med * 3.5 + med_dat.QCW * 3600 * 2 + (Qr + med_dat.Qb) * 3.5 + med_dat.Qdist * 1) / (1000 * npump)) * 1e5 / 3600 / 1000  # kWh

    # Specific energy consumption (electrical) per m3 feed
    SEC_el = E_el_med / (Qf_med / d)  # kWh/m3 feed

    # Specific energy consumption (electrical) per m3 product (distilled water)
    SEC_el_prod = E_el_med / (med_dat.Qdist / 1000)  # kWh/m3 dist water
    
    # Total thermal energy consumption
    E_th_med = med_dat.Q_Tot
    # Specific thermal energy consumption  per m3 feed (kWh_th/m3)
    SEC_th = E_th_med / (Qf_med / d) 

2.7. Use ``output_concentration`` method 
-----
It calculates the ion concentration in the output, g/l. 

.. code-block:: python

    med_dat.output_concentration()

It doesn't take additional inputs. 

**2.7.1. Assigned the results to output parameters** 

You can assigned the results to output parameters: 

.. code-block:: python

    #Concentration of brine stream 
    Cconc_med = [med_dat.CNa_out, med_dat.CCl_out, med_dat.CK_out, med_dat.CMg_out, med_dat.CCa_out, med_dat.CSO4_out]

and calculate the density for brine stream 

.. code-block:: python

    # Calculate density for output concentration
    d_b = density_calc(45, sum(Cconc_med))


2.8. Print results 
-----
You can print results from mass calculations 

.. code-block:: python

    # Flow rates and concentration 
    print("Brine flow rate: "+ str(round(Qout_med,2))+"kg/hr")
    print("Total distillate flow rate: "+ str(round(Qprod_med,2))+"kg/hr") 
    print("Sum of output concentrations: " + str(round(sum(Cconc_med),2))+"g/l")
    print("-----------------------------------------")

    # Calculate energy consumption
    print("Electrical energy consumption: " + str(round(E_el_med,2)) + " kWh")
    print("Specific energy consumption (electrical) per m3 feed: " + str(round(SEC_el,2)) + " kWh/m3")
    print("Specific energy consumption (electrical) per m3 product (distilled water): " + str(round(SEC_el_prod,2)) + " kWh/m3")
    print("-----------------------------------------")


    print("Total thermal energy consumption: " + str(round(E_th_med,2)) + " kW")
    print("Specific energy consumption (thermal) per m3 feed: " + str(round(SEC_th,2)) + " kWh_th/m3")
    print("-----------------------------------------")

    #Calculate required cooling water 
    print("Cooling water flow rate: " + str(round(Qcw,2)) + " kg/hr")
    print("-----------------------------------------")



Brine flow rate: 131.99kg/hr  

Total distillate flow rate: 886.12kg/hr  

Sum of output concentrations: 202.3g/l  

Electrical energy consumption: 1.97 kWh  

Specific energy consumption (electrical) per m3 feed: 2.0 kWh/m3  

Specific energy consumption (electrical) per m3 product (distilled water): 2.22 kWh/m3  

Total thermal energy consumption: 318.96 kW  

Specific energy consumption (thermal) per m3 feed: 324.73 kWh_th/m3  

Cooling water flow rate: 16267.76 kg/hr  
