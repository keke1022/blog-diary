---
layout: post
title:  "WDP归档"
date: 2023-1-8
categories: WDP
---

**This diary is in memory of SJTU WDP, where I went to Fuzhou with my fellows, and where dreams come true.**

## Purpose
Explore the rationality of the supply and demand of medical resources in fever clinic in Jinshan Street in Fuzhou and then determine the next location of a clinic.
<!-- more -->
## Description
The covid-19 virus is getting serious at present. It is obvious that the medical resource could not meet with the increasing need. Therefore, we aim to explore how to distribute medical resource to ensure the health of the population in Jinshan Street to the greatest extent possible. In our project, medical resource is reflected in fever clinics. The overall health condition is evaluated by local severe cases.

## Data needed
- Addresses of communities in Jinshan Street 
- Population of communities in Jinshan Street 
- Scale of fever clinics in Jinshan Street


## Steps
### Part I: Supply and Demand Evaluation
- Simulation of the outbreak for a period
    - Use SEIR model
    - Set parameters to reflect the real situation in a region

- Evaluate whether supply and demand meets
    - Draw the curve of severe cases
    - The slower rate of increase, the lower peak value, the better supply and demand meets

### Part II: Optimal Location of the Next Clinic
- Divide the map into grid
- Take advantage of a heat map to show the effectiveness of each cell as the fifth location

## Discussion
1. Simulation:
   
Due to a lack of data, many assumptions are made, which may not be consistent with the real situation here. This will cause errors. However, those missing information can be acquired somehow for better simulation.
#### Assumptions:
- The pattern of movement between zones within the region in a typical day is randomized
  - Solution: the exchange of population between zones can be obtained by phone data like public transportation usage
- The transition rate between Infected and Recovered is the same for all regions
  - Solution: count the average recovering time for each region, which is related to population structure and clinics scale, etc.

2. Evaluate Supply and Demand relation

Currently, we use the formula peak value for infected cases divided by the time to peak to reflect the seriousness of outbreak in a region. However, we should also take into account factors such as the proportion of severe disease and the extent of economic damage. For example, communities with a large proportion of elderly people should be given priority.

3. Select the location for the fifth clinic

We design the effectiveness of a new location as a formula. $cost=\sum_{i=0}^n w_0((x - x_0)^2 + (y - y_0)^2)$ $w_0$ reflects the seriousness of the outbreak in the community. For the convenience, the clinic should be built nearer to area with serious situation. We aim to minimize the cost value in order to select the optimal location. There is still some room for improvement.
- We divide the Jinshan Street into $n=21$ communities, and treat them as a point with correspoinding population for the sake of convenience. However, a community consists of many residential quarters, and each residential quarter has many buildings. This method can be optimized given real population data of Jinshan Street. In this way, we could treat people in the same building as a whole and build more complex but accurate models 
- The distance between the new clinic and communities are all straight-line distance. However, we should consider public transport development and road condition. For future research, we may take advantage of map software to calculate the real distance.

## Method
1. Simulation of coronavirus outbreak: SEIR model (sample code + pic)
    - The population can be divided into four compartments:   
      - Susceptible (those who can catch the disease)
      - Exposed (those who are exposed to the disease but don’t have symptoms yet)
      - Infected (those who show symptoms and are infective)
      - Recovered (those who have recovered and become immune).
    - The model takes into account following factors, which is reflected in initially set parameters.
      - the transition rate between Susceptible and Exposed
      - the transition rate between Infected and Recovered
      - an incubation period, during which those who are exposed are not infective
      - outbreak starts from an initial 10 infected cases
      - the pattern of movement between zones within the region in a typical day
    - The relationship between Susceptible, Exposed, Infected, Recovered is reflected in differential equations.
      $Susceptible \rightarrow Exposed \rightarrow Infected \rightarrow Recovered$
      - $dS \over dT$
      - eq2
      - eq3
      - eq4

2. Evaluate whether supply and demand meets
   - Draw the curve of infected cases with in the region. The seriousness is defined as the peak value divided by the time of reaching the peak.
      - pic1
3. Decide the location of the fifth clinic based on current simulation
   - Use a mathematical formula to determine the effectiveness of a new location. The lower the value is, the better the location will be.
     - $Effectiveness=\sum_{i=0}^n w_0((x - x_0)^2 + (y - y_0)^2)$
   - Divide the map into grid and calculate the effectiveness for each cell
   - Display the effectiveness in the form of heatmap, which will indicate the optimal location
