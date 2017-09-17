# MPC Project Reflection

### 1. Model Details 
#### 1.1 States
##### Parameters
* **x**: vehicle's position along x-axis. Note that it's actually always  be zero as the calculation is done within vehicle coordinate.
* **y**: vehicle's position along y-axis. Note that it's actually always be zero as the calculation is done within vehicle coordinate.
* **psi**:  vehicle's heading orientation. Note that it's actually always be zero as the calculation is done within vehicle coordinate.
* **v**: vehicle's speed.
* **cte**: cross-track error, representing how far the vehicle is away from desired route.
* **epsi**: orientation error, representing angle the vehicle is away from desired value.

##### Costs
 The costs on vehicle's states include:
 
 * Optimize  **cte** to be closed to zero.
 * Optimize **epsi** to be closed to zero.
 * Optimize **v** to be closed to constant speed (35 by default `ref_v`).
 
#### 1.2 Actuators
##### Parameters
* **delta**: vehicle steering angle.
* **a**: vehicle throttle value.

##### Costs
The costs on vehicle actuators include:

* Optimize **delta** to be closed to zero such that we minize the use of changing steering angle.
* Optimize **a** to be closed to zero such that  we minize the use of throttling.
* Penalize **a** when **delta** goes large and vice versa such that the vehicle handles better on both straight lane and sharp turning.
* Penalize successive **a** changes and **delta** changes such that the vehicle won't suddenly changes the actuators' value.

#### 1.3 Constraints (Update functions)
The constraints and update functions applies to each state predication and it follows the vehicle motion model.

### 2. Timestep Length and Elapsed Duration
Firstly, I set the timestep is set to `25` and the elapsed duration to `0.05` timesteps. The default value already satisfies vehicle control granularity.

Then I noticed that the vehicle is now prediting states for `25 * 0.05 = 1.25s` future which is unnecessary and wasting computation resources, thus I reduce it to `15` timesteps for better performance.

### 3. Polynomial Fitting and MPC Preprocessing
The project transform MPC calculation from map coordinate to vehicle's coordinate for two reasons:

1. Transform default waypoints values to vehicle's coordinate such that we can send and plot the true track  positon values on simulator.
2. The predicted waypoints is also in vehicle's coordinate so we can send the data directly to simuator without doing extra transformation. 

### 4. Model Predictive Control with Latency
This is achieves by predicting `State` 100ms ahead before sending to the MPC for optimization. The details can be found in `main.cpp`(@line 90 - 99) .
