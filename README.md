# Obstacle-Avoidance-Model-for-Drones-NASA

## Introduction
Drones are becoming more popular in a variety of industries, but one of the most difficult challenges in their operation is obstacle avoidance as it is critical for the safe and efficient operation of the drone. A statistical model for obstacle avoidance can help to improve the safety and efficiency of drone operations. The goal of this project is to create a statistical model for obstacle avoidance in drones by combining stepwise variable selection and Bayesian modeling in R Studio. The model aims to help explore the effectiveness of using drones for delivery purposes in various regions.
The dependent variable for our model will be real_height, which represents the height of an obstacle in the map. The independent variables will be map_height, reported_obstacles, map_last_update, and map_source. These variables are related to the drone’s flight path and the detection of obstacles. We will use stepwise variable selection to identify the most important independent variables that affect the real_height of an obstacle in the map. This method will help us to select the most relevant independent variables for our Bayesian model while keeping the model as simple as possible.
Once we have identified the relevant independent variables, we will use Bayesian modeling to develop a probabilistic model of the drone’s movement and obstacle detection. The Bayesian model will allow us to incorporate prior knowledge about the environment and the drone’s capabilities, as well as learn from data collected during drone flights. We will evaluate the performance of our model based on its ability to predict the real_height of obstacles in the map and avoid collisions. Based on the results of the Bayesian model, we will prepare an optimal path for the drone to follow, which avoids obstacles and minimizes travel time. The model can be used to develop better obstacle avoidance algorithms and inform the design of future drone systems.

### This chunk loads all the required packages in R for our project.
library(brms) #For Bayesian multilevel models
##Loading required package: Rcpp
##Loading 'brms' package (version 2.18.0). Useful instructions
##can be found by typing help('brms'). A more detailed introduction
##to the package is available through vignette('brms_overview').
## 
## Attaching package: 'brms'
## The following object is masked from 'package:stats':
## 
##     ar
library(corrplot) #Visualize correlation
## corrplot 0.92 loaded
library(MASS) #Runs stepAIC function
library (ggplot2) #Creates visualization
library(igraph) #Creates graphs
## 
## Attaching package: 'igraph'
## The following objects are masked from 'package:stats':
## 
##     decompose, spectrum
## The following object is masked from 'package:base':
## 
##     union
library(readxl) #Reads and writes in excel
library(BayesFactor) #Used for Bayesian model comparison
## Loading required package: coda
## Loading required package: Matrix
## ************
## Welcome to BayesFactor 0.9.12-4.4. If you have questions, please contact Richard Morey (richarddmorey@gmail.com).
## 
## Type BFManual() to open the manual.
## ************
## 
## Attaching package: 'BayesFactor'
## The following object is masked from 'package:igraph':
## 
##     compare
library(bridgesampling) #Computes log marginal likelihood via bridge sampling
## 
## Attaching package: 'bridgesampling'
## The following object is masked from 'package:brms':
## 
##     bf
library(loo) #For checking WAIC
## This is loo version 2.5.1
## - Online documentation and vignettes at mc-stan.org/loo
## - As of v2.0.0 loo defaults to 1 core but we recommend using as many as possible. Use the 'cores' argument or set options(mc.cores = NUM_CORES) for an entire session.
## - Windows 10 users: loo may be very slow if 'mc.cores' is set in your .Rprofile file (see https://github.com/stan-dev/loo/issues/94).
## 
## Attaching package: 'loo'
## The following object is masked from 'package:BayesFactor':
## 
##     compare
## The following object is masked from 'package:igraph':
## 
##     compare

