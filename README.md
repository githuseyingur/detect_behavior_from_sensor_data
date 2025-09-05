# Detect Behavior From Sensor Data (Helios)
This repository explores time-series classification using multimodal sensor data collected from the Helios wristband, which integrates IMU (motion), ThM (temperature), and ToF (proximity) sensors. The objective is to detect and differentiate body-focused repetitive behaviors (BFRBs), such as hair pulling, from everyday non-BFRB gestures, such as adjusting glasses.

Accurately disentangling these behaviors is an important step toward developing reliable wearable BFRB-detection devices, which have direct applications in mental health monitoring and treatment support.

## What is BFRB?
BFRB (Body-Focused Repetitive Behaviors) are repetitive self-grooming behaviors such as hair pulling, skin picking, or nail biting, which often lead to physical damage and are difficult to control.

## IMU, THM, ToF sensors
### IMU
IMU (Inertial Measurement Unit) sensors are devices that measure the motion and orientation of an object. They typically include:<br>
Accelerometer: Measures linear acceleration along X, Y, and Z axes.<br>
Gyroscope: Measures angular velocity (rotation) around X, Y, and Z axes.<br><br>

>In the competition, the sensors are as follows:<br>
>* 3 accelerometer channels **(acc_x, acc_y, acc_z)**: Measure linear acceleration along the X, Y, and Z axes, capturing the object’s movement and speed.<br>
>* 4 rotation channels **(rot_x, rot_y, rot_z, rot_w)**: Represent the object’s orientation in quaternion format, tracking its angular position and rotation in 3D space.

### THM
Thermopile (THM) sensors are infrared sensors that measure temperature from a distance. They consist of many tiny thermocouples connected in series or parallel, which convert incoming infrared radiation into an electrical signal.
>In this competition, there are 5 THM (thermopile) sensors.

### ToF
Time-of-Flight (ToF) sensors measure the distance to objects by emitting light (usually infrared) and calculating the time it takes for the light to reflect back. This allows them to create depth maps and detect shapes in 3D.<br>

>In the competition, the ToF (Time-of-Flight) sensor has a resolution of **5 × 64 = 320** pixels. Each pixel measures the distance to the object at that point, allowing the sensor to capture depth information across 320 points. This creates a low-resolution 3D representation of the scene.
>In short: The ToF sensor provides 320 depth measurements to capture spatial information.
