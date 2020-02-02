# Estimation Project #


## The Tasks ##

Project outline:

 - [Step 1: Sensor Noise](#step-1-sensor-noise)
 - [Step 2: Attitude Estimation](#step-2-attitude-estimation)
 - [Step 3: Prediction Step](#step-3-prediction-step)
 - [Step 4: Magnetometer Update](#step-4-magnetometer-update)
 - [Step 5: Closed Loop + GPS Update](#step-5-closed-loop--gps-update)
 - [Step 6: Adding Your Controller](#step-6-adding-your-controller)



### Step 1: Sensor Noise ###

Determine the standard deviation of the measurement noise of both GPS X data and Accelerometer X data.

***Success criteria:*** *Your standard deviations should accurately capture the value of approximately 68% of the respective measurements.*

---------------
1. Calculated the standard deviation through STDEVP() in excel

2. Put the calculated value in config/6_Sensornoise.txt
   MeasuredStdDev_GPSPosXY = .68  #2
   MeasuredStdDev_AccelXY = .48  #.1

---------------


### Step 2: Attitude Estimation ###

Implement a better rate gyro attitude integration scheme in the UpdateFromIMU() function.

***Success criteria:*** *Your attitude estimator needs to get within 0.1 rad for each of the Euler angles for at least 3 seconds.*

---------------
Replace small-angle approximation integration method in function UpdateFromIMU() by:

1. Apply FromEuler123_RPY function for creating a quaternion (qt) from Euler Roll/PitchYaw
2. Apply IntegrateBodyRate(gyro, dtIMU) to get q_t (formula (43) in 7.1.2 Nonlinear Complementary filter)
3. Extract roll, pitch, yaw from q_t
4. Normalize yaw to -pi .. pi

---------------


### Step 3: Prediction Step ###

Implement all of the elements of the prediction step for the estimator.

***Success criteria:*** *The prediction step should include the state update element (PredictState() function), a correct calculation of the Rgb prime matrix, and a proper update of the state covariance. The acceleration should be accounted for as a command in the calculation of gPrime. The covariance update should follow the classic EKF update equation.*

---------------
PredictState()
1. Apply attitude.Rotate_BtoI(<V3F>) to rotate a vector from body frame to inertial frame
2. Update predictedState() through 
   predictedState(pos) = curState(pos) + dt * curState(vel)
   predictedState(vel) = curState(vel) + dt * curState(accel)

GetRbgPrime()
1. Put elements in RbgPrime (formula (52) in 7.2 Transition Model)

Predict()
1. Put elements in gPrime (formula (51) in 7.2 Transition Model)
2. Update the ekfCov according to the EKF equation (Algorithm 2 Predict)

---------------



### Step 4: Magnetometer Update ###

Implement the magnetometer update.

***Success criteria:*** *Your goal is to both have an estimated standard deviation that accurately captures the error and maintain an error of less than 0.1rad in heading for at least 10 seconds of the simulation.*

---------------
UpdateFromMag()
1. zFromX(0) = ekfState(6);
2. calculate and normalize difference between measured and estimated yaw

---------------


### Step 5: Closed Loop + GPS Update ###

Implement the GPS update.

***Success criteria:*** *Your objective is to complete the entire simulation cycle with estimated position error of < 1m.*

---------------
UpdateFromGPS()
1. Put elements in hPrime and estimate (formula (53-55) in 7.3.1 GPS)

---------------


### Step 6: Adding Your Controller ###


***Success criteria:*** *Your objective is to complete the entire simulation cycle with estimated position error of < 1m.*

---------------
1. Replace `QuadController.cpp` with the controller you wrote in the last project.

2. Replace `QuadControlParams.txt` with the control parameters you came up with in the last project.

3. Run scenario `11_GPSUpdate`. If your controller crashes immediately do not panic. Flying from an estimated state (even with ideal sensors) is very different from flying with ideal pose. You may need to de-tune your controller. Decrease the position and velocity gains (we’ve seen about 30% detuning being effective) to stabilize it.  Your goal is to once again complete the entire simulation cycle with an estimated position error of < 1m.
---------------


References:
1. Estimation for Quadrotors，https://www.overleaf.com/read/vymfngphcccj (really helpful to have a better understanding of the algorithms for EKF)
2. https://github.com/mixmasteru/FCND-Estimation-CPP, https://github.com/eric-y-chen/fcnd-estimation-cpp