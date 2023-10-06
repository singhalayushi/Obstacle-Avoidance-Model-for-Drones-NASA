# Obstacle-Avoidance-Model-for-Drones-NASA

## Introduction
Drones are becoming more popular in a variety of industries, but one of the most difficult challenges in their operation is obstacle avoidance as it is critical for the safe and efficient operation of the drone. A statistical model for obstacle avoidance can help to improve the safety and efficiency of drone operations. The goal of this project is to create a statistical model for obstacle avoidance in drones by combining stepwise variable selection and Bayesian modeling in R Studio. The model aims to help explore the effectiveness of using drones for delivery purposes in various regions.
The dependent variable for our model will be real_height, which represents the height of an obstacle in the map. The independent variables will be map_height, reported_obstacles, map_last_update, and map_source. These variables are related to the drone’s flight path and the detection of obstacles. We will use stepwise variable selection to identify the most important independent variables that affect the real_height of an obstacle in the map. This method will help us to select the most relevant independent variables for our Bayesian model while keeping the model as simple as possible.
Once we have identified the relevant independent variables, we will use Bayesian modeling to develop a probabilistic model of the drone’s movement and obstacle detection. The Bayesian model will allow us to incorporate prior knowledge about the environment and the drone’s capabilities, as well as learn from data collected during drone flights. We will evaluate the performance of our model based on its ability to predict the real_height of obstacles in the map and avoid collisions. Based on the results of the Bayesian model, we will prepare an optimal path for the drone to follow, which avoids obstacles and minimizes travel time. The model can be used to develop better obstacle avoidance algorithms and inform the design of future drone systems.

** This chunk loads all the required packages in R for our project. **


```
library(brms) #For Bayesian multilevel models
library(corrplot) #Visualize correlation
library(MASS) #Runs stepAIC function
library (ggplot2) #Creates visualization
library(igraph) #Creates graphs
library(readx) #Reads and writes in excel
library(BayesFactor) #Used for Bayesian model comparison
library(bridgesampling) #Computes log marginal likelihood via bridge sampling
library(loo) #For checking WAIC
```

## Reading the data

** This code chunk reads the contents of the “map info.csv” file in the local directory. The functions used in this chunk are: a) read.csv() - Helps to read the csv file and stores the data in a dataframe map_df b) as.factor() - Converts the map_source column of the map_df dataframe into a factor variable using the as.factor() function. This is done when the data in a column represents categorical data, and for using a categorical data column as interaction, we convert it to a factor. **

```
map_df = read.csv("C:\\College\\Quarter 2\\OPS 804 - Advanced Data Analysis\\Project\\map_info.csv")
map_df$map_source=as.factor(map_df$map_source)
```

## Learning about the dataset by using summary and str functions

```
summary(map_df) #Display summary statistics of the variables in the data frame.
```

```
str(map_df)     #Displays the structure of the data frame.
```

```
head(map_df,20) #Display the first 20 rows of the data frame.
```

## Checking the correlation

**This code chunk is used to visualize the correlation between selected variables in the map_df dataset. The resulting correlation plot provides a quick overview of the strength and direction of the relationships between the variables.
**

```
#Load required packages
library(ggplot2)
library(dplyr)
```


