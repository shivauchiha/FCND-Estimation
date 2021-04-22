## Project: 3D Control


---


# Required Steps for a Passing Submission:
1. Implemented body rate control in C++. 
2. Implement roll pitch control in C++.
3. Implement altitude controller in C++.
4. Implement lateral position control in C++.
5. Implement yaw control in C++.
6. Implement calculating the motor commands given commanded thrust and moments in C++.
7. Tune the parameters to pass all five scenarios.
8. Write up


## [Rubric](https://review.udacity.com/#!/rubrics/1643/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it! Below I describe how I addressed each rubric point and where in my code each point is handled.


### Implementing Your Control Algorithm

#### 1. Implemented body rate control in C++.
The job of this function is to provide appropriate momentum cmds for the drone to perform to achieve control objective.These momentum cmd follow the control law


	(pqrCmd-pqr)*Inertia*kpPQR;



As you can see the cmds are a function of desired pitch , roll and yaw rates.The moments of inertia are calculated based on mass and form factor of the drone.[List of moments of inertia](https://en.wikipedia.org/wiki/List_of_moments_of_inertia)
 

#### 2. Implement roll pitch control in C++.
This is the function that generates roll and pitch rates to follow based on desired global x,y acceleration and thrust.It takes in the drones attitude to calculate desired error and applies it to the following control law.


	pqrCmd.x = (R(1,0) * b_x_p_term - R(0,0) * b_y_p_term) / R(2,2);
  	pqrCmd.y = (R(1,1) * b_x_p_term - R(0,1) * b_y_p_term) / R(2,2);
  	pqrCmd.z = 0;  


Here R elements are related to rotation matrix that helps in conversion between global and drone frame.b_x_p and b_y_p are related to function of commanded acceleration in global x-y frame.
Care is also taken in such a way the tilt angle generated does not exceed drones capacity.

#### 3. Implement altitude controller in C++.
This part of the controller is responsible for maintain the desired altitude of the drone.This takes in desired velocity and position along z axis and generates required thrust. This controller follows the below control law.

	u_1_bar = p_term + d_term + i_term +accelZCmd;

This is basically a feed forward PID controller. P_term is related to position error,d_term is related to velocity error and i_term is related to accumulated position error. This control is translated into thrust by following equation.
 
	thrust = -1*mass*(u_1_bar - 9.8f)/b_z;
 

#### 4. Implement lateral position control in C++.
This controller is responsible for generating desired accelerations in global xy co-ordinate system based on desired position and velocity. The control law is a basic PD feed forward controller.A checking condition is also implemented to make sure the cmd_velocity is within the technical capabilites of the drone. Following control law is followed.

	accelCmd = (kpPosXY*pos_error)+(kpVelXY*vel_error)+accelCmdFF;

#### 5. Implement yaw control in C++.
This is a simple P controller that controls the yaw of drone.It outputs desired yaw rates . Limit checking and efficient direction of turn conditions are implemented. The following control law is implemented.

    
        yawRateCmd = kpYaw* psi_err;

#### 6. Implement calculating the motor commands given commanded thrust and moments in C++. 
This part of the controller takes in commanded thrust and desired momentum command and generates  the desired thrust of the motor. The equation is based on following dynamics of the drone.

        float arm = L / sqrt(2.f);
  	cmd.desiredThrustsN[0] = 1/4.f * (collThrustCmd + (momentCmd.x / arm) + (momentCmd.y / arm) + (-momentCmd.z / kappa)); 
  	cmd.desiredThrustsN[1] = 1/4.f * (collThrustCmd - (momentCmd.x / arm) + (momentCmd.y / arm) - (-momentCmd.z / kappa)); 
  	cmd.desiredThrustsN[2] = 1/4.f * (collThrustCmd + (momentCmd.x / arm) - (momentCmd.y / arm) - (-momentCmd.z / kappa));
  	cmd.desiredThrustsN[3] = 1/4.f * (collThrustCmd - (momentCmd.x / arm) - (momentCmd.y / arm) + (-momentCmd.z / kappa));






