# Detect Behavior From Sensor Data (Helios Wristband)
This repository explores time-series classification using multimodal sensor data collected from the Helios wristband, which integrates IMU (motion), ThM (temperature), and ToF (proximity) sensors. The objective is to detect and differentiate body-focused repetitive behaviors (BFRBs), such as hair pulling, from everyday non-BFRB gestures, such as adjusting glasses.

Accurately disentangling these behaviors is an important step toward developing reliable wearable BFRB-detection devices, which have direct applications in mental health monitoring and treatment support.

## What is BFRB?
BFRB (Body-Focused Repetitive Behaviors) are repetitive self-grooming behaviors such as hair pulling, skin picking, or nail biting, which often lead to physical damage and are difficult to control.

## IMU, THM, ToF sensors
### IMU
IMU (Inertial Measurement Unit) sensors are devices that measure the motion and orientation of an object. They typically include:<br>
Accelerometer: Measures linear acceleration along X, Y, and Z axes.<br>
Gyroscope: Measures angular velocity (rotation) around X, Y, and Z axes.<br><br>

>In the dataset, the sensors are as follows:<br>
>* 3 accelerometer channels **(acc_x, acc_y, acc_z)**: Measure linear acceleration along the X, Y, and Z axes, capturing the object’s movement and speed.<br>
>* 4 rotation channels **(rot_x, rot_y, rot_z, rot_w)**: Represent the object’s orientation in quaternion format, tracking its angular position and rotation in 3D space.

```python 
cols = [col for col in test.columns if col.startswith('acc') or col.startswith('rot')]
train[cols]
```

<img width="547" height="191" alt="Ekran görüntüsü 2025-09-06 111656" src="https://github.com/user-attachments/assets/108009b0-52bd-49d9-bde2-b3ccff8059a3" />


### THM
Thermopile (THM) sensors are infrared sensors that measure temperature from a distance. They consist of many tiny thermocouples connected in series or parallel, which convert incoming infrared radiation into an electrical signal.
>In this dataset, there are 5 THM (thermopile) sensors.

```python 
thm_cols = [col for col in test.columns if col.startswith('thm')]
train[thm_cols]
```
<img width="419" height="187" alt="thm" src="https://github.com/user-attachments/assets/0192c362-525f-4907-887d-91f2deaf2b2f" />


### ToF
Time-of-Flight (ToF) sensors measure the distance to objects by emitting light (usually infrared) and calculating the time it takes for the light to reflect back. This allows them to create depth maps and detect shapes in 3D.<br>

>In the dataset, the ToF (Time-of-Flight) sensor has a resolution of **5 × 64 = 320** pixels. Each pixel measures the distance to the object at that point, allowing the sensor to capture depth information across 320 points. This creates a low-resolution 3D representation of the scene.
>In short: The ToF sensor provides 320 depth measurements to capture spatial information.

```python 
tof_cols = [col for col in test.columns if col.startswith('tof')]
train[tof_cols]
```
  
<img width="1429" height="217" alt="tof" src="https://github.com/user-attachments/assets/2916c6c4-2bfa-4e18-b09e-5b015d86f247" />


## GESTURES (Target)
### BFRB-Like Gestures
* Above ear - Pull hair
* Forehead - Pull hairline
* Forehead - Scratch
* Eyebrow - Pull hair
* Eyelash - Pull hair
* Neck - Pinch skin
* Neck - Scratch
* Cheek - Pinch skin


### Non-BFRB-Like Gestures (Non-Target Gestures)
* Drink from bottle/cup
* Glasses on/off
* Pull air toward your face
* Pinch knee/leg skin
* Scratch knee/leg skin
* Write name on leg
* Text on phone
* Feel around in tray and pull out an object
* Write name in air
* Wave hello

## DATA
### Dataset Composition

- The dataset consists of **81 subjects** and **8,151 motion sequences**.
- There are **18 gesture classes** in total, including both **target (BFRB-related)** and **non-target** gestures.
- All subjects perform **all gesture types**, and each gesture is repeated **multiple times per subject**.
- A subset of gestures corresponds to **BFRB behaviors**, while others are **non-BFRB control gestures**.
- The distinction between *Target* and *Non-Target* gestures is provided only in the **training set** via the `sequence_type` feature and is **not available in the test set**.

### Sequence Types and Metadata (Train-only)

The following metadata values are **present only in the training set** and do **not appear in the test set**:

- `sequence_type`: Target, Non-Target  
- `phase`: Transition, Gesture  
- `orientation`:  
  - Seated Straight  
  - Seated Lean Non-Dominant – Face Down  
  - Lie on Back  
  - Lie on Side – Non-Dominant  
- `behavior`:  
  - Perform gesture  
  - Moves hand to target location  
  - Hand at target location  
  - Relaxes and moves hand to target location  

These features cannot be used at inference time and were treated with care to avoid data leakage.

### Sequence Length Statistics

Motion sequences are variable-length. The distribution of sequence lengths (`SEQ_LEN`) is as follows:

| Statistic | Value |
|---------|-------|
| Count   | 8,151 |
| Mean    | 70.54 |
| Std     | 35.39 |
| Min     | 29 |
| 25%     | 51 |
| Median  | 59 |
| 75%     | 78 |
| Max     | 700 |

This large variance in sequence length makes **temporal modeling and attention mechanisms especially important**.

### Handedness and Data Cleaning

- **12 subjects are left-handed**.
- **2 subjects were identified with incorrectly mounted devices**, indicated by a consistently negative mean in the `acc_y` signal.
- These 2 subjects were **excluded from training** to prevent systematic noise.

### Train / Validation Split Strategy

- Data was split **by subject**, ensuring that no subject appears in both training and validation sets.
- Left-handed subjects were **evenly distributed** across folds to maintain balance.
- This strategy reflects a realistic generalization scenario and prevents subject-level leakage.

---

## Gesture Distribution

### Total Motion Samples per Gesture  
*(Including all time steps across sequences)*

| Gesture | Total Samples |
|-------|---------------|
| Text on phone | 58,462 |
| Neck – scratch | 56,619 |
| Eyebrow – pull hair | 44,305 |
| Forehead – scratch | 40,923 |
| Forehead – pull hairline | 40,802 |
| Above ear – pull hair | 40,560 |
| Neck – pinch skin | 40,507 |
| Eyelash – pull hair | 40,218 |
| Cheek – pinch skin | 40,124 |
| Wave hello | 34,356 |
| Write name in air | 31,267 |
| Pull air toward your face | 30,743 |
| Feel around in tray and pull out an object | 17,114 |
| Glasses on/off | 13,542 |
| Drink from bottle/cup | 13,093 |
| Scratch knee/leg skin | 12,328 |
| Write name on leg | 10,138 |
| Pinch knee/leg skin | 9,844 |

### Number of Sequences per Gesture

| Gesture | # Sequences |
|-------|-------------|
| Forehead – scratch | 640 |
| Text on phone | 640 |
| Forehead – pull hairline | 640 |
| Neck – scratch | 640 |
| Neck – pinch skin | 640 |
| Eyelash – pull hair | 640 |
| Above ear – pull hair | 638 |
| Eyebrow – pull hair | 638 |
| Cheek – pinch skin | 637 |
| Wave hello | 478 |
| Write name in air | 477 |
| Pull air toward your face | 477 |
| Feel around in tray and pull out an object | 161 |
| Write name on leg | 161 |
| Pinch knee/leg skin | 161 |
| Scratch knee/leg skin | 161 |
| Drink from bottle/cup | 161 |
| Glasses on/off | 161 |

---

## Observations

- **All 81 subjects perform all 18 gestures**, but the **number of repetitions varies significantly** between gestures.
- BFRB-related gestures are generally **repeated more frequently per subject** than non-BFRB gestures.
- Average repetitions per subject per gesture:

| Gesture | Avg. Repetitions |
|-------|------------------|
| Above ear – pull hair | 7.88 |
| Cheek – pinch skin | 7.86 |
| Eyebrow – pull hair | 7.88 |
| Eyelash – pull hair | 7.90 |
| Forehead – pull hairline | 7.90 |
| Forehead – scratch | 7.90 |
| Neck – pinch skin | 7.90 |
| Neck – scratch | 7.90 |
| Text on phone | 7.90 |
| Wave hello | 5.90 |
| Write name in air | 5.89 |
| Pull air toward your face | 5.89 |
| Drink from bottle/cup | 1.99 |
| Glasses on/off | 1.99 |
| Scratch knee/leg skin | 1.99 |
| Write name on leg | 1.99 |
| Pinch knee/leg skin | 1.99 |
| Feel around in tray and pull out an object | 1.99 |
<img width="1920" height="578" alt="train" src="https://github.com/user-attachments/assets/563191ef-102b-4834-9c98-6fe6cc7c58e2" />

### TRAIN DATA
* row_id
* sequence_id - An ID for the batch of sensor data. Each sequence includes one Transition, one Pause, and one Gesture.
* sequence_type - If the gesture is a target or non-target type. Train only.
* sequence_counter - A counter of the row within each sequence.
* subject - A unique ID for the subject who provided the data.
* gesture - The target column. Description of sequence Gesture. Train only.
* orientation - Description of the subject's orientation during the sequence. Train only.
* behavior - A description of the subject's behavior during the current phase of the sequence.
* acc_[x/y/z] - Measure linear acceleration along three axes in meters per second squared from the IMU sensor.
* rot_[w/x/y/z] - Orientation data which combines information from the IMU's gyroscope, accelerometer, and magnetometer to describe the device's orientation in 3D space.
* thm_[1-5] - There are five thermopile sensors on the watch which record temperature in degrees Celsius. Note that the index/number for each corresponds to the index in the photo on the Overview tab.
* tof_[1-5]_v[0-63] - There are five time-of-flight sensors on the watch that measure distance. In the dataset, the 0th pixel for the first time-of-flight sensor can be found with column name tof_1_v0, whereas the final pixel in the grid can be found under column tof_1_v63. This data is collected row-wise, where the first pixel could be considered in the top-left of the grid, with the second to its right, ultimately wrapping so the final value is in the bottom right (see image above). The particular time-of-flight sensor is denoted by the number at the start of the column name (e.g., 1_v0 is the first pixel for the first time-of-flight sensor while 5_v0 is the first pixel for the fifth time-of-flight sensor). If there is no sensor response (e.g., if there is no nearby object causing a signal reflection), a -1 is present in this field. Units are uncalibrated sensor values in the range 0-254. Each sensor contains 64 pixels arranged in an 8x8 grid, visualized in the figure below.

###  DEMOGRAPHIC DATA
* subject
* adult_child: Indicates whether the participant is a child (0) or an adult (1). Adults are defined as individuals aged 18 years or older.
* age: Participant's age in years at time of data collection.
* sex: Participants sex assigned at birth, 0= female, 1 = male.
* handedness: Dominant hand used by the participant, 0 = left-handed, 1 = right-handed.
* height_cm: Height of the participant in centimeters.
* shoulder_to_wrist_cm: Distance from shoulder to wrist in centimeters.
* elbow_to_wrist_cm: Distance from elbow to wrist in centimeters.

* 
