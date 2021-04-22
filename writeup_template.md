## Project: Estimation


---


# Required Steps for a Passing Submission:
 1. Sensor Noise
 2. Attitude Estimation
 3. Prediction Step
 4. Magnetometer Update
 5. Closed Loop + GPS Update
 6. Adding Your Controller


## [Rubric](https://review.udacity.com/#!/rubrics/1807/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it! Below I describe how I addressed each rubric point and where in my code each point is handled.


### Implementing Your Estimation Algorithm

#### 1. Sensor Noise 
In this section we try to model the sensor noise of both GPS X data and Accelerometer X data . Basically find the standard deviation in such a way that it captures 68% of sensor measurements. Here I used pandas to read log data and used .std() functionality of the data to find standard deviation of our sensor model this was then set in config file and tested.
 

#### 2. Attitude Estimation
We improve the integration scheme using non linear complimentry filter . The concept is based in eq43 of [document](https://www.overleaf.com/project/5c34caab7ecefc04087273b9).Here we convert estimated roll,pitch and yaw from euler form to quaternion form then we use already available integrate function to integrate gyro rates into the system.Them using roll , pitch information from accelerometers we implement a complementary filter as described under attitude estimation section of same document mentioned above.All the coding is under UpdatefromIMU() function.



#### 3. Prediction Step
Here we write a function that generated RBG prime matrix used in udate step of extended kalman filter.Equation 52 of the document is used to write the matrix. After this under predict function a projection algorithm is implemented as part of ekf update steps that moves state of the system forward in time . This uses the gprime matrix that uses RBG prime matrix which we wrote earlier. Using this we predict the covariance . please note state prediction step was already called in code template.


#### 4. Magnetometer Update.
Here we define hprime,state from previous predicted state and using magnetometer covariance matrix call an update function to sensor fuse magnetometer information to our state specifically yaw part of the state . The math for update step is already defined in Algorithm 2 part of EKF section.

#### 5. Closed Loop + GPS Update.
Here we define hprime,state from previous predicted state and using GPS covariance matrix call an update function to sensor fuse GPS information to our state specifically x,y,z and its associated velocities part of the state . The math for update step is already defined in Algorithm 2 part of EKF section.

#### 6. Adding Your Controller. 
The old control code was replaced with one i wrote in my previous project.Surprisingly didnt have to tune much as my old parameters itself was enough to pass the evaluation

---

### Conclusion
All criteria was passed by evaluator.






