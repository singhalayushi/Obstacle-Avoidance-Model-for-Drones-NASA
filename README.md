# Obstacle-Avoidance-Model-for-Drones-NASA

### Introduction
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

### Reading the data

** This code chunk reads the contents of the “map info.csv” file in the local directory. The functions used in this chunk are: a) read.csv() - Helps to read the csv file and stores the data in a dataframe map_df b) as.factor() - Converts the map_source column of the map_df dataframe into a factor variable using the as.factor() function. This is done when the data in a column represents categorical data, and for using a categorical data column as interaction, we convert it to a factor. **

```
map_df = read.csv("C:\\College\\Quarter 2\\OPS 804 - Advanced Data Analysis\\Project\\map_info.csv")
map_df$map_source=as.factor(map_df$map_source)
```

### Learning about the dataset by using summary and str functions

```
summary(map_df) #Display summary statistics of the variables in the data frame.
```

```
str(map_df)     #Displays the structure of the data frame.
```

```
head(map_df,20) #Display the first 20 rows of the data frame.
```

### Checking the correlation

**This code chunk is used to visualize the correlation between selected variables in the map_df dataset. The resulting correlation plot provides a quick overview of the strength and direction of the relationships between the variables.
**

```
#Load required packages
library(ggplot2)
library(dplyr)
library(ggcorrplot)
```

```
map_df_subset <- map_df[, c("real_height","map_height", "reported.obstacles","map_last_update","map_source")]
```

```
#Creating scatterplots for all the dependent vs independent variables
ggplot(map_df_subset, aes(x = map_height, y = real_height)) + 
  geom_point() + 
  labs(x = "Map Height", y = "Real Height")
```
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/bc8805f9-8fc2-4d23-8ac3-7556d0b94b66)

```
#Creating scatterplot for reported_obstacles with real_height
ggplot(map_df_subset, aes(x = reported.obstacles, y = real_height)) + 
  geom_point() + 
  labs(x = "Reported Obstacles ", y = "Real Height")
```
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/c4105ee6-e162-454a-bf43-9912c33192ee)

```
#Creating scatterplot for map_last_update with real_height
ggplot(map_df_subset, aes(x = map_last_update, y = real_height)) + 
  geom_point() + 
  labs(x = "Map last update", y = "Real Height")
```
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/1999798c-2174-4bb2-b026-e5b9d6657d0a)

```
#Creating scatterplot for interactions - map_source
ggplot(map_df_subset, aes(x = map_source, y = real_height)) + 
  geom_point() + 
  labs(x = "Map Source", y = "Real Height")
```
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/64983706-224f-44e8-a958-429d56025ed2)

```
# Creating a heatmap of the correlation matrix
correlation_matrix <- select_if(map_df_subset, is.numeric) %>% cor()
ggcorrplot(correlation_matrix, hc.order = TRUE, type = "upper", lab = TRUE)
```
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/a68309fe-7659-4a0f-86ef-0d27777d3bc6)

### Running variable selection methods(Stepwise Method) on the linear regression models

The stepAIC() function is used in this code chunk to perform a stepwise variable selection method. The goal is to find a subset of predictor variables that best predicts the response variable real height in the linear regression model and the best subset with the variables will be further used in the Bayesian model.

```
fullModel = lm(real_height ~ map_height * map_source + map_source + reported.obstacles + map_last_update, data=map_df)
intercept_model = lm(real_height~1, data=map_df)
step.model_both <- stepAIC(intercept_model, scope=formula(fullModel),  direction="both", trace = TRUE)
```

```
step.model_both
```

```
step.model_both$coefficients
```

### Finalizing the best model to use in the Bayesian Model
Further three Bayesian models will be created using the brm() function. The first model includes all the independent variables, while the second and third models use the recommended variables and recommended variables with interactions, respectively.

### Making the bayesian model and checking if it has converged
Here, the dependent variable is real_height, and independent variables are map_height + reported.obstacles + map_last_update.

### Bayesian Model with all independent variables

```
brm_mdl = brm(real_height ~ map_height + reported.obstacles + map_last_update , data = map_df, iter = 2000, warmup = 200, chains = 3, thin = 2 )
```

### Printing summary statistics
```
summary(brm_mdl)
```

### Visualizing the posterior distributions of the model parameters and diagnostics of the model fit.
```
plot(brm_mdl)
```
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/fd81ef5c-8330-4d5d-ac90-3c795e7577af)

**The conditional_effects function is used to plot the estimated marginal effects of the predictor variables on the response variable, with 'points = TRUE' adding the observed data points to the plot.
**
```
plot(conditional_effects(brm_mdl),points = TRUE)
```
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/662fa1e7-8763-4e14-890a-74e6f76bc1cf)
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/cf0110fc-d443-4835-9387-ea8648800f7d)
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/12f07736-b0a7-4db1-badf-bfe8492ce826)

### Bayesian Model with the recommended variables suggested by Variable Selection Method
```
brm_mdl_1 = brm(real_height ~ map_height + map_source + reported.obstacles , data = map_df, iter = 2000, warmup = 200, chains = 3, thin = 2 )
```
```
summary(brm_mdl_1)
```
```
plot(brm_mdl_1)
```
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/41da27eb-c9a4-4da8-8113-7f1d0fc43a8b)
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/a3bee2b2-7e09-436c-b009-4443557e230e)

```
plot(conditional_effects(brm_mdl_1),points = TRUE)
```
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/51cda9ef-5dfa-4166-89d9-22cee4a36558)
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/caebd2cd-fb8c-4fee-983b-ef8e95fd2bb9)
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/1fe08fcd-8d0a-48fd-b0cf-f880a8c45f60)

### Bayesian Model with the recommended variables suggested by Variable Selection Method with interactions
```
brm_mdl_2 = brm(real_height ~ map_height * map_source + reported.obstacles , data = map_df, iter = 2000, warmup = 200, chains = 3, thin = 2 )
```

```
summary(brm_mdl_2)
```

```
plot(brm_mdl_2)
```
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/a3eac98f-2db3-402d-948e-0cfc2173b266)
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/bc7adfb3-f0b7-436f-8b01-f49ca89926c5)

```
plot(conditional_effects(brm_mdl_2),points = TRUE)
```
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/f06471e8-3154-4dce-b45b-01d601544ec2)
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/0a9e00bd-74c9-4621-89c9-7cb0f50f4ffd)
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/2bba033d-72d4-4dc8-b245-8ebd473ed50e)
![image](https://github.com/singhalayushi/Obstacle-Avoidance-Model-for-Drones-NASA/assets/123263574/f063045b-90dc-4155-a931-52c0cdab868a)

### Comparing the models using WAIC method
```
waic_brm_mdl <- waic(brm_mdl)$waic
```

```
print(paste("WAIC for the model with all the independent variables",waic_brm_mdl))
```

```
print(paste("WAIC for the model with the recommended variables",waic_brm_mdl_1))
```

```
print(paste("WAIC for the model with recommended variables alongwith interactions",waic_brm_mdl_2))
```

```
loo(brm_mdl,brm_mdl_1)
```
**
This chunk sets up the initial parameters for creating a binary map, which will identify obstructed areas for a drone to fly over. We can sets the maximum height for an obstacle and defines the acceptable probability threshold for avoiding obstructed areas according to the business requirement and urgency; like lesser the probability value, lesser the risk and we are then following the conservating approach.**

#initial setting
data_row_n = nrow(map_df)
data_col_n = ncol (map_df)

# initializing the binary map
binary_map = rep (0, nrow = data_row_n)

#It is assumed that drones have a limitation on flying at altitudes that exceed 'obstacle_height' feet. Hence, any square on the map that has a height greater than 'obstacle_height' feet is considered an obstruction for the drone. Here, we assumed that drones cannot fly over 60 feet.

obstacle_height = 60 # a height that will be considered as an obstacle for a drone

# If the probability that the square ij contains an obstacle is 'acceptable_prob' percent or higher, then we will steer clear of square ij. The acceptable probability threshold can be modified depending on the mission objectives, whether it is a riskier or more conservative mission.

acceptable_prob = .22 

```
#Calculating the posterior distribution of the brm_mdl (assign the name ‘post’ to it) using posterior_samples function
post = posterior_samples(brm_mdl)
## Warning: Method 'posterior_samples' is deprecated. Please see ?as_draws for
## recommended alternatives.
summary(post)
```
