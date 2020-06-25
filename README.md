# A2C-single-car-intersection
　This is a basic model describing a car runs to goal in limited time by using A2C algorithm to control its speed.    
　Now, My purpose is building a architecture at frist, which I can use later. By the A2C I wrote I'll write a A3C model in the next time, in which I'll complete a multi-agents system(MAS).
# Reward shaping
　The work in this model is very simple.   
　Input [real_speed, target_speed, elapsed_time_ratio, distance_to_goal,reward,done,time_pass,over]    
　Output accelerate.
  
　The car will learn to control its accelerate with the restructions shown below:  
　Reward shaping:  
* rt = r terminal + r danger + r speed  
* r terminal： -0.13：crash / time expires 
                 0.005:non-terminal state  
* r speed： related to the target speed  
* if sa ≤st: 0.05 - 0.036*(target_speed / real_speed) 
* if sa > st: 0.05 - 0,033*(real_speed / target_speed ) 

　In my experiment it's obviously I desire the agent to learn controling its speed around the target-speed.    
 　parameters:
# About GAMA
　The GAMA is a platefrom to do simulations.      
　I have a GAMA-modle "simple_intersection.gaml", which is assigned a car and some traffic lights. The model will sent some data  
　[real_speed, target_speed, elapsed_time_ratio, distance_to_goal,reward,done,time_pass,over]  
　as a matrix to python environment, calculating the car's accelerate by A2C. And applying to the Markov Decision Process framework, the car in the GAMA will take up the accelerate and send the latest data to python again and over again until  reaching the destination.
# Architecture
　The interaction between the GAMA platform and python environment is built on csv files IO. So GAMA model needs to use R-plugin and the R environment needs package "reticulate" to connect with python (I use python more usually).
 
  ![image](https://github.com/ZHONGJunjie86/A3C-single-car-intersection/blob/master/illustrate/illustrate.gif )   
  A2C-architecture
  --------------
  ![image](https://github.com/ZHONGJunjie86/A3C-single-car-intersection/blob/master/illustrate/A2C-Architecture.JPG) 
  A3C-architecture
  ------------
  ![image](https://github.com/ZHONGJunjie86/A3C-single-car-intersection/blob/master/illustrate/A3C-Architecture.JPG) 
  # Experiment
  ###### gama:
           time_target <- int((distance_left/100)*5)+ rnd(3); 
           target_speed<- distance_left/time_target;
           random_node <- int(rnd(12));
           target<- node_agt(random_node);
           true_target <- node_agt(random_node);
           final_target <- node_agt(random_node).location;	
           location <- any_location_in(node_agt(5)); 
　There are 12 nodes in the intersection map and the start point is fixed at the 5th point. Every time before a cycle there will be a random number between 0 and 12 used to choose destination node. And the target-time and target speed will also be changed.   
　In other words, I let the agent to learn 3*11=33 situations.   
 # MC
 <a href="https://www.codecogs.com/eqnedit.php?latex=\bigtriangledown&space;R&space;=&space;\frac{1}{N}\sum_{n=1}^{N}\sum_{t=1}^{T}(R(t)-V_{s}^{n})\bigtriangledown&space;log&space;P_{\Theta&space;}(a_{t}^{n}|s_{t}^{n})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\bigtriangledown&space;R&space;=&space;\frac{1}{N}\sum_{n=1}^{N}\sum_{t=1}^{T}(R(t)-V_{s}^{n})\bigtriangledown&space;log&space;P_{\Theta&space;}(a_{t}^{n}|s_{t}^{n})" title="\bigtriangledown R = \frac{1}{N}\sum_{n=1}^{N}\sum_{t=1}^{T}(R(t)-V_{s}^{n})\bigtriangledown log P_{\Theta }(a_{t}^{n}|s_{t}^{n})" /></a>
　The target parameters need careful adjustment. Here's the curves of entropy and rewards.    
  
 ![image](https://github.com/ZHONGJunjie86/A2C-TD-single-car-intersection/blob/master/illustrate/loss_curve_MC.png)
      
