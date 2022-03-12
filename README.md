# Writeup: Track 3D-Objects Over Time

### 1.1 Write a short recap of the four tracking steps and what you implemented there (filter, track management, association, camera fusion). Which results did you achieve? 

## filter

This section involved developing a KF/EKF for 3D tracking for a target, which will extend later to multiple targets. 

![P_T1_1](https://user-images.githubusercontent.com/23568809/158014749-ca2e9170-8360-4faa-9601-f057de322c24.png)
![P_T1_2](https://user-images.githubusercontent.com/23568809/158014758-b5c6a2cb-72a7-4d2c-8d73-12045d8b6187.png)

## track management

Here, the code that keeps track of other detected cars/objects through a track state was developed. The code defines initializing tracks, updating the score based on measurements, and deleting tracks if/when required. 

https://user-images.githubusercontent.com/23568809/158014957-697a4e99-d18e-417b-b8d7-8168f0028789.mp4

![P_T2_1](https://user-images.githubusercontent.com/23568809/158014763-2f7f9186-cc06-4f06-944d-4d4c3cfe37eb.png)

## association

Linking measurements to tracks is done here. Mahalanobis distance was the measure used to do this.

https://user-images.githubusercontent.com/23568809/158014956-bfc1b9ae-85c8-4734-a9c4-544cd0b68e91.mp4

![P_T3_1](https://user-images.githubusercontent.com/23568809/158014810-c227b7d3-8fd9-4793-8661-9744db67a47f.png)


## camera fusion

Here, the camera model is used in addition to the lidar measurements to gain as much information as possible about the scenario to the tracking filter.

#trial 1 - track management issues incountered

https://user-images.githubusercontent.com/23568809/158017531-f03298c0-01eb-454a-9428-c80c5c95dda1.mp4
 
#trial 2 - issues fixed

https://user-images.githubusercontent.com/23568809/158017617-8e4541a0-e380-45fd-9def-d16a1ee000f2.mp4


![P_T4_1](https://user-images.githubusercontent.com/23568809/158017533-d4bb9ab4-2772-43df-8741-fb19378789b5.png)

### 1.2 Which part of the project was most difficult for you to complete, and why?

Given my background, the estimation part was most trivial. Keeping track of all the classes used to implement different functionality was more cumbersome. Specifically, I found integrating the camera fusion with the track management to be challenging, but that was due to few hasty coding decisions on my part. Other than that, I found this project more enjoyable than not.

### 2. Do you see any benefits in camera-lidar fusion compared to lidar-only tracking (in theory and in your concrete results)? 

In theory, the more information available, the better the estimation performance. In practice, this is a significant computational load. In my results, testing shows a drop in the average RMSE. However, one track exhibited a small rise in RMSE. I believe the track management can do with more tuning and/or development. Also, the detection algorithm can probably sustain more performance improvement.

### 3. Which challenges will a sensor fusion system face in real-life scenarios? Did you see any of these challenges in the project?

- Measurements coming at different rates
- Inaccuracies in the underlying system model
- Sensor failures
  - Physical failure
  - Environmental visibility degredation 
- Noise covariance magnitude instability/drift

In this project, a simplistic constant velocity model was used, data was assumed to come at a constant rate, and noise statistics were assumed to be known a priori with some level of certainty.

### 4. Can you think of ways to improve your tracking results in the future?

- A real kinematic model can be used
- INS/GPS use/correction to localize the vehicel is neccessary 
- While the EKF is an industry standard, other nonlinear filters with similar computational burden exist and can be substituted seemlessly 
- A better track management soloution can be used
- A stero-vision soloution can aid in aquiring more information about the surroundings of the vehicle



