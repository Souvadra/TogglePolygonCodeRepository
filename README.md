# Article: Operating principles of circular toggle polygons

# Instruction to the end-user to replicate the results

## RACIPE Simulations (Three independent replicates for each network)

Detailed instructions about RACIPE is given [here:](https://www.google.com "RACIPE Code Repo")

The information of the network and its interactions are written in a .topo file. For details, visit the above link. 
After the topo file is created, the compiled RACIPE can be run using: 
`./RACIPE <file>.topo -num_paras <number_of_parameter_sets> -num_ode <number_of_initial_conditions_for_each_parameter_set>` 
Additinal flags were used in special conditions, for those details, refer to "Methods" section or our article and visit the above mentioned repository of RACIPE.

Once the triplicates of the RACIPE simulations are done, we use the following pipelines to analyze the data in different ways.

**Important**: Keep all the triplicates of the RACIPE solutions for a same network in the same directory (preferebly, name the directory as the name of the network). For example Make a directory `/home/user1/4c` and keep the replicate one inside the the folder as `/home/user1/4c/1` and same for replicate two and three. 

# Code definitions: 

**G/K Normalization:**  
filename: `GK_normalization.md`

This code performs G/K normalization on all the RACIPE solution files present in the Given directory and writes the output in the `*_solutions_gk_?.dat` files in the same directory.

inputs: 
1. `path`: path where the RACIPE solution files i.e. `*_solution_?.dat` files are stored. 
2. `components_num`: Number of components in the network. Example: `components_num = 4` for network 4c, 4cS, 
3. `external_signal`: Always equal to Zero for all the analysis we have performed in this article. Hence, `external_signal = 0`

Example: `GK_normalization("path_to_RACIPE_simulations/4c/2",4,0)`

***

**Stability State Counting** 
filenames: `MakeStabilityStateCounter.m, stabilityStateCounter.m` <-- For default RACIPE files, which performs calculations till "deca-stable" solutions. 
   `universal_stability_state_counter.m, stateCounter.m` <-- for more complex RACIPE simulations file, where we specify to calculate beyond "deca-stable solutions"

This set of codes basically reads the RACIPE solutions files (of the triplicates for a particular network) and calculates what percentage of the total number of solutions belong to mono-stable, bi-stable, tri-stable and "higher-stable" solutions. This stores this data, in the form of a .fig and .xls file, which can be later used to plot the data. 
   The data contains, the mean of the frequency of the each solution states, found in each of the triplicates and the standard deviation is calculated for the error-bar. 

---

_Main Functions:_

_For default RACIPE solutions:_ i.e. `-num_stability 10` --> `MakeStabilityStateCounter.m `

inputs: 
1. `path`: path of the directory of the network, where all the triplicates of the RACIPE simulations results are kept. 
2. `Name`: `<Name>_stability_state_counter`will be the name of the .fig and .xls file, which will be generated as the output of this code. 

Example: `MakeStabilityStateCounter('path_to_RACIPE_simulations/5cS','5cS')`

---
_For larger RACIPE solutions:_ e.g. `-num_stability 20` -->   `universal_stability_state_counter.m`

inputs:
1. `p1`: path of the first replicate of the RACIPE simulation of the concerned network 
2. `p2`: path of the second replicate of the RACIPE simulation of the concerned network 
3. `p3`: path of the third replicate of the RACIPE simulation of the concerned network 
4. `name`: `<name>_stability_state_counts<30>` is the name of the .fig file it generates as output. [Note, this code does not generate a separate .xls file, although the .fig file can be used to extract the mean and standard deviation data using standard MATLAB functions]

Example: `universal_stability_state_counter('path_to_longer_RACIPE_simulations/7cS/1','path_to_longer_RACIPE_simulations/7cS/2','path_to_longer_RACIPE_simulations/7cS/3','7cS')`

***

**Overall Frequency Calculations**
filename: `allSolutionCombiner.m`

This function takes all the solution files of a RACIPE simulation, starting from the monostable solution to the highest-stable solution present in the directory, and merge those to form a combined solution files, which mimic that of a mono-stable solution file, but containes solution from all the stability states. The output of this file is a .dat file, which is printed in the same directory. Basically, this function makes a solutions file, which mimics that of a Boolean solution file, for the same network, for further downstream analysis. This output file(.dat) also requires G/K normalization for further analysis. 

inputs:

1. `path`: path where the RACIPE solution files i.e. `*_solution_?.dat` files are stored.
2. `components_num`: Number of components in the network.
3. `Name`: the name of the ouput .dat file. 

example: `allSolutionFileCombiner('path_to_RACIPE_simulations/9cS/2/', 9, '9cS_combined')`

---

filenames: 
   Main Function: `make_an_errorbar_conditions_apply.m`
   Helper Function: `err_bar_maker.m`

Main Function: This function reads the solution files, mentioned in the inputs and output the average frequency and the standard deviation across all the triplicates of the different solution states in that solution file. The output result is stored in a .xls file, which can be later analysed using Microsoft Excel. 

inputs:

1. `p1`: path of the solution file of our choice of stability state in the first replicate of the RACIPE simulation of the concerned network 
2. `p2`: path of the solution file of our choice of stability state in the the second replicate of the RACIPE simulation of the concerned network 
3. `p3`: path of the solution file of our choice of stability state in the the third replicate of the RACIPE simulation of the concerned network 
4. `sol_num`: number of stabiliy-state. e.g. `2` for bistable states, `10` for decastable states
5. `components_num`: Number of components in the network
6. `ext_signals_num`: always = `0` for all the relevant simulations done in this study 
7. `condition`: the output will only show sates with frequency, larger than the magnitude mentioned in this variable. In our case, we used `condition = 0` universally, and then later used Microsoft Excel to pick the most dominant states later from the output .xls file. 
8. `name`: Name of the output .xls file. 

example: `make_an_errorbar_conditions_apply('/path_to_RACIPE_simulations/7c/1/7c_solution_gk_10.dat','/path_to_RACIPE_simulations/7c/2/7c_solution_gk_10.dat', '/path_to_RACIPE_simulations/7c/3/7c_solution_gk_10.dat', 10, 7, 0, 0,'7c_deca_stable')`

---

filenames: 
   Main Function: `most_common_odd_mono_state_finder.m`
   Helper Function: `err_bar_maker.m`

Main Function: ONLY WORKS WITH ODD-NUMBERED TOGGLE POLYGON NETWORKS. It reads all the monostable solution files from each of the triplicates of the RACIPE simulations and make a .xls file that states the relative frequency of the all the dominant monostable states compared to the rest of the states in the monostable solution.

inputs: 

1. `p1`: path of the monostable state solution file in the first replicate of the RACIPE simulation of the concerned network 
2. `p2`: path of the monostable state solution file in the the second replicate of the RACIPE simulation of the concerned network 
3. `p3`: path of the monostable state solution file in the the third replicate of the RACIPE simulation of the concerned network 
4. `components_num`: Number of components in the network. 
5. `name`: Name of the output .xls file. 

example: `most_common_odd_mono_state_finder('path_to_RACIPE_simulations/9c/1/9c_solution_gk_1.dat','path_to_RACIPE_simulations/9c/2/9c_solution_gk_1.dat', 'path_to_RACIPE_simulations/9c/3/9c_solution_gk_1.dat', 9,'9c_mono_common')

---

filenames: 
   Main Function: `most_common_odd_bi_state_finder.m`
   Helper Function: `err_bar_maker.m`

Main Function: ONLY WORKS WITH ODD-NUMBERED TOGGLE POLYGON NETWORKS. It reads all the bistable solution files from each of the triplicates of the RACIPE simulations and make a .xls file that states the relative frequency of the all the bistable states generated via some combination of the dominant monostable states compared to the rest of the states in the bistable solution.

inputs:

1. `p1`: path of the bistable state solution file in the first replicate of the RACIPE simulation of the concerned network 
2. `p2`: path of the bistable state solution file in the the second replicate of the RACIPE simulation of the concerned network 
3. `p3`: path of the bistable state solution file in the the third replicate of the RACIPE simulation of the concerned network 
4. `components_num`: Number of components in the network. 
5. `name`: Name of the output .xls file. 


example: `most_common_odd_bi_state_finder('path_to_RACIPE_simulations/9c/1/9c_solution_gk_2.dat','path_to_RACIPE_simulations/9c/2/9c_solution_gk_2.dat', 'path_to_RACIPE_simulations/9c/3/9c_solution_gk_2.dat', 9,'9c_bi_common')`

---

**JSD (Jensen-Shannon Divergence) Calculations**
filename: `CombinedcsvGen.m`

This function takes the frequency distribution of the combined results of the RACIPE and Boolean simulations and make a combined matrix that displays the predicted frequencies of each solution states as per RACIPE and Boolean simulations. This output matrix is saved as a .csv file, which we are going to use later for calculation of the JSD of those two distributions. 

inputs:

1. `pathRacipe`: path to the combined frequency distribution file generated after analysing the RACIPE simulation data 
2. `pathBoolean`: path to the combined frequency distribution file generated in Boolean simulation
4. `name`: Name of the output .csv file.

example: `CombinedcsvGen('RACIPE/3c_combined.csv','Boolean/Boolean_freq_3c.csv','3c_RACIPE_plus_Boolean')`

--- 

filename: `JSD-operation.ipynb`

This is a very small Jupyter Notebook script that prompts the user to input the frequency distrubtion of the combined RACIPE simulation and then the frequency distribution of the Boolean simulation. Then it calculates the JSD between the two distributions to find out how much similar those distrubutions are. The input data that the program prompts for can be manually copy-pasted in the terminal (when it prompts for it) from the output .csv file of the `CombinedcsvGen.m` function. 

---
---


