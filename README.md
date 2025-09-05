# Detect Behavior From Sensor Data (Helios)
This repository explores time-series classification using multimodal sensor data collected from the Helios wristband, which integrates IMU (motion), ThM (temperature), and ToF (proximity) sensors. The objective is to detect and differentiate body-focused repetitive behaviors (BFRBs), such as hair pulling, from everyday non-BFRB gestures, such as adjusting glasses.

Accurately disentangling these behaviors is an important step toward developing reliable wearable BFRB-detection devices, which have direct applications in mental health monitoring and treatment support.

## What is BFRB?
BFRB (Body-Focused Repetitive Behaviors) are repetitive self-grooming behaviors such as hair pulling, skin picking, or nail biting, which often lead to physical damage and are difficult to control.

## IMU, THM, ToF sensors
#### IMU
acc_x, acc_y, acc_z: Linear acceleration on X, Y, Z axes.
rot_x, rot_y, rot_z, rot_w: Orientation (quaternion components) from the IMU.

#### THM
Thermopile (THM) sensors are infrared sensors that measure temperature from a distance. They consist of many tiny thermocouples connected in series or parallel, which convert incoming infrared radiation into an electrical signal.

#### ToF
Time-of-Flight (ToF) sensors measure the distance to objects by emitting light (usually infrared) and calculating the time it takes for the light to reflect back. This allows them to create depth maps and detect shapes in 3D.
If a ToF sensor has 5 × 64 = 320 pixels, it means it captures depth information with 320 independent measurement points (like a low-resolution depth camera). Each pixel represents a distance value, enabling the sensor to build a small 3D image of the scene.
