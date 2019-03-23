# Model Proposal for _Shamalee Goonetilleke_

_Shamalee Goonetilleke_

* Course ID: CMPLXSYS 530,
* Course Title: Computer Modeling of Complex Systems
* Term: Winter, 2019



&nbsp; 

### Goal and Justification
*****
 
_Provide a short, 1-3 sentence description of the goal of your model_

To study how bacteria gain antibiotic reistance when exposed to variable levels of antibiotic in localized niches. One theory of how bacteria develop reistance is via stress induced mutagensis which increases the overall mutation rate in bacterial genomes via the selective use of error prone polymerases ( i.e. DNA replicators). However, this theory has detractors as they argue that if overall mutation rate increases then the rate of detrimental mutations, which should be more abundant, will also increase and cancel out the beneficial effects. Through ABM modelling I hope to be able to test out different models of mutagensis applied to a system created by Robert Austin et al. in the paper " Acceleration of emergence of bacterial antibiotic resistance in connected microenvironments" to see if I can recreate thier experimental results. 

&nbsp;  

### Main Micro-level Processes and Macro-level Dynamics of Interest
****

_Short overview of the key processes and/or relationships you are interested in using your model to explore. Will likely be something regarding emergent behavior that arises from individual interactions_

Evolution of resistance over time in a hetrogenous environment:
Micro level processes - Incude the bacteria surviving or dying in the different niches as they using ei11   ther thier existing genome or thier mutated 'genome' as a source of information to make these desicions. 
Macro level processes - The spread of the reistant bacteria over the whole community in spread of bacteria from high to low concentration. 

&nbsp; 

*__LS COMMENTS:__ Good overview of goals. Watch for some typos here.*

## Model Outline
****
&nbsp; 
### 1) Environment
_Description of the environment in your model. Things to specify *if they apply*:_
In general the basic model will consist of a grid of squares for each of which the interior represents a place where a bacterium can occupy. This lines in the grid will represent walls made of silicon through which 'ports' will be made to represent slits that allow nutrient and bacteria flow between chambers. The entire grid will be filled with evenly dispersed nutrient. At the top and bottom of the grid there will be head space which also have nutrient and one end will additionally contain some initialized concentration of antibiotic that can then diffuse through the grid thus creating a gradient. 

######  _Boundary conditions (e.g. wrapping, infinite, etc.)_
- There will be no wrapping as the edges of the world will act as solid silicon walls thus no bacteria, nutrients or antibiotics should be able to flow through.  
###### _Dimensionality (e.g. 1D, 2D, etc.)_
- The model is 2D a grid. This is simplified to make computational processing easier but the effect of the grid depth may be relevent. In the paper they used (hexagonal) grids with a depth of 10um.   
###### _List of environment-owned variables (e.g. resources, states, roughness)_
- nutrient concentration - Although as we're modelling the effect of antibiotic on mutation it might be good to remove this variable as otherwise it would be hard to distinguish why theyre living or dying.
- region i.e. As the grid is seperated into distinct regions which will eventually have varying conditions its important for the patches     to know which region they belong to.
- Antibiotic concentration
- port-type i.e. is it a input or output port ( e.g. antibiotic or bacteria can enter/leave from the port). This variable will only be relevent to patches which form the walls between grids. The could easily be distinguished by giving these patches different colors. Also, not all patches in the walls need have a port-type as varying the size of the slit through which bacteria/nutrients/antibiotics can pass would be interesting to model as well.  

###### _List of environment-owned methods/procedures (e.g. resource production, state change, etc.)_
- antibiotic diffusion and dffusion rate




```
# Include first pass of the code you are thinking of using to construct your environment
# This may be a set of "patches-own" variables and a command in the "setup" procedure, a list, an array, or Class constructor
# Feel free to include any patch methods/procedures you have. Filling in with pseudocode is ok! 
# NOTE: If using Netlogo, remove "python" from the markdown at the top of this section to get a generic code block

Using a varient of the many regions code and the bacteria infection code which already exists in netlogo 
- set up a user controlled number of regions 
- draw boundaries a width/number-of-regions and height/number of regions by coloring the patches
- color all patches inside regions with specific color
-give all patches in one region the same number for variable called region number 
- Make subset of patches in boundary the same color as the patches inside the region so that agents can be told to only move on patches of that color

;;;;;;;;;;; The code below only chnages the conc in ONE reservoir NOT both - the bottom one remains constant at a set value which in this case is -100;;;;;;;;;
;;unchanging conc is the sink through which diffused antibiotic liquid is removed and the changing conc is the variable antibiotic gradient
; see under questions to be answered below
patches-own 
[ antibiotic 
anti-antibiotic]

to setup
clear-all
  ask patches with [pxcor < -15] [
    set antibiotic  antibiotic-conc 
  set pcolor scale-color black antibiotic -100 100
  ]
  
  ask patches with [pxcor > 15] [
    set anti-antibiotic (-100)
  set pcolor scale-color black anti-antibiotic -100 100
  ]
  
reset-ticks
end

to go


  ask patches with [pxcor < -15] [
    set antibiotic  antibiotic-conc 
  ;set pcolor scale-color black antibiotic -100 100
  ]
diffuse antibiotic 0.1
 
   ask patches with [pxcor > 15] [
    set antibiotic (-100)
  ;set pcolor scale-color black anti-antibiotic -100 100
  ]
diffuse anti-antibiotic 0.1
 
recolor
 
  
  
tick
end

to recolor
 ask patches [set pcolor scale-color black (anti-antibiotic + antibiotic) -100 100]
end
```

&nbsp; 

*__LS COMMENTS:__ Well thought out design here and good initial pass at some of the code needed to capture environment processes. Making sure that the "diffuse" processes only occur through ports and not across walls looks like it will be an important issue to solve. Also, scale of regions to the size of bacteria will be important to think about. Doesn't need to be exact, but will definitely want to shoot for something in the arena of the same magnitudes you are dealing with empirically.*

### 2) Agents
 
 _Description of the "agents" in the system. Things to specify *if they apply*:_
 
 The agents in the system will be bacteria. The base model of bacteria can be created such that beyond a certain antibiotic concentration they die or else they live and then use that as a benchmark to study the distribution of bacteria. Then different varients of this model can be tested:
 1. Random mutation case: With a certain probability, which will be equal to the probability of a random mutation occuring in a genome the size of an average bacterial genome, the bacteria will develop resistance such that they can survive the higher concentrations of antibiotic. They will also have a certain probability of dying that is also determined by random mutations 
 2. Stress induced mutagenesis: With a certain probability, n times (this is still to be determined from the literature) higher than in case 1, the bacteria develop resistance to the higher concentration of antibiotic. However, with a probability >> than the probability of developing resistance, the bacteria can also develop a mutation that either ouright kills them or at least makes them more likely to die. 
 3. Localized stress induced mutagenesis: With a certain probability (equal to that in case 2?) the bacteria develop resistance to antibiotic but have no enhanced death rate other than that caused by aging/ random mutations.
 4. Other edge cases such as modelling fragile genomic sites. 
 
###### * _List of agent-owned variables (e.g. age, heading, ID, etc.)_
- region that the agent is intiated in. 
- Mutability (which can be dependent on the local antibiotic concentration)
- Number of replications : this can determine when they wil die of natural aging e.g. after 5 replications the cell can die. 
###### * _List of agent-owned methods/procedures (e.g. move, consume, reproduce, die, etc.)_
- Eat: Use up local nutrients/ food source  could (may or may not be used in the final model)
- death: Die below a certain nutrient concentration and/or after a certain number of mutations. 
- move: Move between grids through the output/input ports. 
- reproduce: Reproduce and have the offsrping be formed at a (random) slight angle so that the bacteria don't form a long linear chain. 


```
# Include first pass of the code you are thinking of using to construct your agents
# This may be a set of "turtle-own" variables and a command in the "setup" procedure, a list, an array, or Class constructor
# Feel free to include any agent methods/procedures you have so far. Filling in with pseudocode is ok! 
# NOTE: If using Netlogo, remove "python" from the markdown at the top of this section to get a generic code block


```

&nbsp; 

*__LS COMMENT__: Well thought out design. Do need to see some first pass code here ideally, however, even if it is pseudocode. Will movement be random or will it be driven by food and/or avoiding antibiotics? Is it possible that bacteria will run out of space to reproduce in a region?*

### 3) Action and Interaction 
 
**_Interaction Topology_**

###### _Description of the topology of who interacts with whom in the system. Perfectly mixed? Spatial proximity? Along a network? CA neighborhood?_

- No antibiotic in the system at t=0 except at the intialized headspace. Once time starts the bacteria will diffuse across the grid.
 
 *__LS COMMENTS__: Are the bacteria affected by the presence of other bacteria nearby?*
 
**_Action Sequence_**

###### _What does an agent, cell, etc. do on a given turn? Provide a step-by-step description of what happens on a given turn for each part of your model_

1. Bacteria sense local antibiotic concentration and decide to live or die
2. Mutate using one of the rule sets described previously. Which can lead to:
  a. Antibiotic survival phenotype
  b. No change in antibiotic susceptibility
  c. Death 
3. Reproduce and if reproduce_number > 5 then die
4. Move randomly to a new unoccupied region.
(The order for 2 and 3 seems arbitary but once the model is set up it will be useful to flip them around to see if order does play a role in how the bacteria distribute across the environment


&nbsp; 

*__LS COMMENTS:__ See that movement is random, which I think is great and should be easy to capture. Like the way you are handling mutation - very smart. I assume reproduced bacteria will inherit resistance? Will resistant bacteria ever mutate to lose their resistance? Another question - do movement and reproduction usually occur on the same timescale with bacteria? I assume movement happens a lot more quickly than reproduction, but I could definitely be wrong about that! If they aren't on the same timescale, I would suggest making reproduction happen with some appropriate probability each turn or have it happen once every certain number of turns.* 

### 4) Model Parameters and Initialization

###### _Describe and list any global parameters you will be applying in your model._

- The region dimensions i.e. the location and number of grids where bacteria can exist in the world. 

*__LS COMMENTS:__ Good. Concentration and diffusion of antibiotic and food might also be considered global parameters.*

###### _Describe how your model will be initialized_

Antibiotic in one headspace are initialized at a user variable concentration and bacteria at a user determined concentration are randomly distributed across the surface. 


###### _Provide a high level, step-by-step description of your schedule during each "tick" of the model_

1. bacteria sense antibiotic concentration at thier location and live or die 
2. antibiotic diffuses (see below under questions to be answered for alternative versions)
3. patches update antibiotic concentration
3. bacteria mutates
4. bacteria reproduces
5. number of reproductions is logged for each bacterium 
5. bacteria moves 

###### Questions to be answered:

-Does the antibiotic need to diffuse? Can I just create a prebuilt gradient across the surface but then its diicult to model the effects of varying antibiotic concentration in the head space on the spread of bacteria. An alternative might be to have 2 diffusing species i.e. the antibiotic and anti-antibiotic which each diffuse from opposite ends such that total antibiotic concentration is the sum of antibiotic conc +  anti-antibiotic conc. and the concentration of the opposite species in the headspaces are forced to conc = 0. 
-Should there be memory i.e. should a certain number of past mutations be remembered such that its a combination of them that will lead to death and/or resistance with a certain probability or should we assume the probability accounts for all types of mutations - both single and large scale- 
&nbsp; 

*__LS COMMENTS:__ Good questions! I suggest going with what you think you get to work best and fastest in the immediacy and then refining further if you have time. Along those lines, feel free to go with the simpler versions first and then make more complicated as needed.*

### 5) Assessment and Outcome Measures

###### _What quantitative metrics and/or qualitative features will you use to assess your model outcomes?_

- population of resistant bacteria in the population over time 
- The distribution of resistant bacteria over time this can be reported by induvidual patches in each region,by giving each patch in a region a variable called e.g. region_number, and summed together for each region to find the total number of bacteria in a region . 

&nbsp;



### 6) Parameter Sweep

_What parameters are you most interested in sweeping through? What value ranges do you expect to look at for your analysis?_

- Initial antibiotic concentration
- mutation probabilities

*__LS COMMENTS:__ Very solid proposal! Let me know if you want to talk through some more of the potential design specifics as you dig into the coding further here. 18.5/20*
