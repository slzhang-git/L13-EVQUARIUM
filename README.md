# EVQUARIUM: An EV charging infrastructure evaluation tool

### 1 Introduction

This script gives EVQUARIUM, an EV charging infrastructure evaluation tool with M/D/C charging station queueing. The tool evaluates EV charging infrastructure in a region given the EV charging demand within that region. The tool outputs the expected wait time (queueing+charging) and utilization ratio of each charging station, and average access time (traveling+queueing) of each EV parking location. It could be used to evaluate current or designed EV charging station configurations. 

EVQUARIUM do the evaluation by obtaining the assignment matrix at user equilibrium (UE), which describes which charging stations the EVs at each EV parking location choose to go to. At UE, charging stations used by the EVs from the same origin should have the smallest and the same access time (traveling+queueing). Potantial queueing at charging stations is considered through a M/D/C queueing model. 

For more details, please refer to the following paper:

Liu, B., Pantelidis, T. P., Tam, S., & Chow, J. Y. (2022). An electric vehicle charging station access equilibrium model with M/D/C queueing. International Journal of Sustainable Transportation, 1-17.
https://www.tandfonline.com/doi/full/10.1080/15568318.2022.2029633?casa_token=nifG6rFSWJkAAAAA%3AhUGkUbnVrHglR6tK7j4RrsitSpFIU0yPHM3aKQLEEVe78_GbXLFeHZ6ZBZ0x7cFp0Q0qd541fuYDWQ

### 2 License
The NYU NON-COMMERCIAL RESEARCH LICENSE is applied to EVQUARIUM (attached in the repository). Please contact [Joseph Chow](https://github.com/jc7373) (joseph.chow@nyu.edu) or Christopher Snyder (Christopher.Snyder@nyulangone.org) for commercial use.

For questions about the code, please contact: [Bingqing (Chloe) Liu](https://github.com/BingqingChloeLiuNYU) (bingqing.liu@nyu.edu).


### 3 Instructions

To use this Tool:

Please save the EV location list, charging station list and the travel cost matrix as csv files in the same folder as this script, then fill in the file names in the following cell correspondingly.

1. Prepare EV location csv file
    
    Columns:
    
        "ID" : 1,2,3,4,... (int64)
    
        "Number of EVs" : the number of EVs to be charged per unit time at that location (int64)
    
2. Prepare Charging Station csv file

    Note: Within each charging station, all the chargers are of the same type (Level 2 or DC Fast). If some charging stations have both Level 2 and DC Fast chargers, please seperate each of them as 2 charging stations at the same location.
    
    Columns:
    
        "ID" : 1,2,3,4,... (int64)
        
        "miu": service rate (number of EVs charged/unit time) of one charger at this charging
        station (int64)
        
        "Number of Chargers" : the number of chargers at this charging station (int64)
        
3. Prepare OD cost Matrix csv file

    This matrix should be the travel distance matrix. 
    
    Note:
    
        1) The sequence of the rows should correspond to the sequence of the EV Locations in the EV location csv file, and the sequence of the columns should correspond to the sequence of the charging station csv file (float64)
        2) No heading and index in the csv file

4. Set Average Travel Speed

    Parameter "v": average travel speed of the EV's.
    
    Note: Unit of the speed should be: unit of distance for OD cost/unit of time for service rate and number of EVs to be charged  
    
5. Set Weights for Access Time and Charging Time

    Parameter "access_time_weight":  weight of access time. Recommended value is 6.198.
    
    Parameter "charging_time_weight":  weight of charging time. Recommended value is 1.
    
    For the recommended values, please refer to Ge 2019.
    
    Reference: Ge, Y. (2019). Discrete Choice Modeling of Plug-in Electric Vehicle Use and Charging Behavior Using Stated Preference Data (Doctoral dissertation).

6. Set Convergence Criteria

    Parameter "e":  If the Euclidean norm of the difference between the assignment matrix of this iteration and the last iteration is less than e, the algorithm stops and outputs the results.  

7. Set Printing

    Parameter "pri": print error, mean cost and number of steady-state charging stations every  pri number of iterations. If no printing is needed before the final results, set pri as inf (pri = np.inf)

8. Run the algorithm
    After the algorithm converges with the convergence criterion that you set, please check if all the charging stations are at steady state. 
    The 6 output files are saved in a folder named "Results" where this script is saved. 
    
!!! Please make sure that there's no folder named "Results" at the directiry where the script and input files are saved. 
    The output files include:
    
     - Assignment Matrix.csv
     - Access Time Matrix.csv
     - Charging Time Matrix.csv
     - Access + Charging Time Matrix.csv
     - Charging Station Table.csv
         - Columns:“Utilization Ratio”, 
                   “Expected Queue Delay”, 
                   “Charging Time”, 
                   “Expected Queue Delay + Charging Time”
     - EV Parking Location Table.csv
         - Columns:“Average Access Time”, 
                   “Average Charging Time”, 
                   “Average Access +Charging Time”
    
    The following results are directly printed:
     - System Total Access Time
     - System Total Access Time + Charging Time
     - Average Access Time for one EV
     - Average Access Time + Charging Time for one EV
     - Number of steady-state (utilization ratio ≤ 1) charging stations at convergence 
     - Number of iterations until convergence
     - Run time of the algorithm (wall time)
     
!!! Please check if the number of steady-state charging stations at convergence equals to the total number of charging stations. If a large number of charging stations are not at steady state, the results are not accurate.
Run the algorithm below:



### 4 Example (for our experiment, run the file "EV Assignment Tool (Example and Instructions) - 20210419.py" directly)
We use a small example with 5 EV parking locations (demand nodes) and 3 charging stations to illustrate how the example works.

EV parking location table:

<img width="264" alt="Screen Shot 2023-02-14 at 12 51 33 PM" src="https://user-images.githubusercontent.com/75587054/218817859-09c0a6e5-8cd3-4b1b-ace3-004c9e699869.png">

Charging station table:

<img width="364" alt="Screen Shot 2023-02-14 at 12 51 43 PM" src="https://user-images.githubusercontent.com/75587054/218817893-9d2bc9c0-0f7b-4c30-9729-915b28f5832e.png">

Their locations plotted:

<img width="522" alt="Screen Shot 2023-02-14 at 12 52 43 PM" src="https://user-images.githubusercontent.com/75587054/218818095-80596015-cb4a-429f-9515-e61b94d106d9.png">

The travel cost matrix is computer using linear distance assuming a speed of 20.

Convergence criterion is set as 10^(-4). Printing setting is to print every 10000 iterarions.

Output:

<img width="431" alt="Screen Shot 2023-02-14 at 12 55 11 PM" src="https://user-images.githubusercontent.com/75587054/218818600-78f9f86b-d9d3-4b68-ace7-3effaaf7000a.png">
<img width="486" alt="Screen Shot 2023-02-14 at 12 55 22 PM" src="https://user-images.githubusercontent.com/75587054/218818628-af7e03d0-9bfc-4070-b977-8a2002386975.png">

The output includes error, mean cost of accessing and charging, and number of charging stations at steady state every 10000 iterations. Final assignment matrix and travel+queueing+charging table are printed afterwards.

Convergence is reached after 36869 iterations. Computation time is 11.1 sec.

From the results, we can see Wardrop's principles. For a parking location, the chosen charging stations have the smallest and the same cost of travel+queueing+charging.

Detailed results can be illustrated as follows. For parking locations, average cost of accessing assigned charging stations is computed.

<img width="286" alt="Screen Shot 2023-02-14 at 12 56 14 PM" src="https://user-images.githubusercontent.com/75587054/218818805-b4b1b6cd-2c14-4a7e-a762-d8276dd7f04c.png">
<img width="516" alt="Screen Shot 2023-02-14 at 12 56 24 PM" src="https://user-images.githubusercontent.com/75587054/218818837-401e99d2-a03c-496f-a385-fafcb65c814f.png">

For charging stations, utilization ratio and average queueing+charging time is computed.

<img width="371" alt="Screen Shot 2023-02-14 at 12 56 32 PM" src="https://user-images.githubusercontent.com/75587054/218818872-05d30af2-9919-4790-b9a9-3d4f7249d807.png">
<img width="519" alt="Screen Shot 2023-02-14 at 1 58 38 PM" src="https://user-images.githubusercontent.com/75587054/218831695-baede02b-4140-47e2-ad63-240293bc7d6e.png">
<img width="520" alt="Screen Shot 2023-02-14 at 12 56 54 PM" src="https://user-images.githubusercontent.com/75587054/218818958-4abe3a16-7bc9-45fe-a1a2-65d66fa092cb.png">


### 5 Application

EVQUARIUM could be a useful tool for private sector that deal with EVs, such as charging infrastructure companies and EV mobility providers, in several ways:

1. Planning: EVQUARIUM can help these companies plan their charging station locations and configurations by evaluating the expected demand for EV charging infrastructure in a given region. This information could be used to strategically locate charging stations in high-demand areas, and optimize the configuration of charging stations to minimize wait times and maximize utilization.

2. Performance evaluation: EVQUARIUM can be used to evaluate the performance of existing EV charging infrastructure in a region. By analyzing the expected wait time, utilization ratio, and average access time of charging stations and EV parking locations, companies can identify areas for improvement, such as the addition of more charging stations or optimizing the queueing model to reduce wait times.

3. Investment decisions: EVQUARIUM can provide companies with data and insights to inform investment decisions related to EV charging infrastructure. By evaluating the demand for EV charging infrastructure in a region, companies can make more informed decisions about where to invest their resources, and what types of infrastructure to prioritize.

Overall, EVQUARIUM can help EV-related private sectors to optimize their charging infrastructure, improve the user experience for EV drivers, and make more informed decisions about where and how to invest their resources.

EVQUARIUM could also be a useful tool for public agencies, such as city or state Departments of Transportation (DOT), in several ways:

1. Policy planning: EVQUARIUM can provide public agencies with data on the demand for EV charging infrastructure in a given region, which could inform policy decisions related to EV adoption and infrastructure investment. By understanding the expected demand for EV charging infrastructure, public agencies can make more informed decisions about where to prioritize infrastructure investments and what types of policies to implement to encourage EV adoption.

2. Infrastructure planning: EVQUARIUM can help public agencies plan the location and configuration of EV charging infrastructure. By analyzing the expected demand for EV charging infrastructure, the tool can help public agencies strategically locate charging stations in high-demand areas and optimize the configuration of charging stations to minimize wait times and maximize utilization.

3. Performance evaluation: EVQUARIUM can be used to evaluate the performance of existing EV charging infrastructure in a region. By analyzing the expected wait time, utilization ratio, and average access time of charging stations and EV parking locations, public agencies can identify areas for improvement, such as the addition of more charging stations or optimizing the queueing model to reduce wait times.

4. Climate and energy planning: EVQUARIUM can help public agencies plan for the impacts of increased EV adoption on climate and energy systems. By understanding the expected demand for EV charging infrastructure, public agencies can plan for the infrastructure and energy needs that will arise as more EVs come online, and develop policies and programs to promote the use of clean energy sources for EV charging.

Overall, EVQUARIUM can provide public agencies with data and insights to inform policy and infrastructure planning related to EV adoption and infrastructure investment, as well as support efforts to reduce greenhouse gas emissions and promote the use of clean energy sources.

Thank you!


