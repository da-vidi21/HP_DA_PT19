# Drona Aviation Pluto Drone Swarm Challenge

 Python wrapper for intraction with official Pluto Drone's firmware API. This package has following features:

**Task-1**

- [x] Implementation of control commands for the drone movements.(such as Pitch forward, Roll
left, take off - Land, etc.)

**Task-2**

- [x] Overhead camera calibration method
- [x] ArUCo tag getection and tracking
- [x] Estimation of pose of the ArUCo tag
- [x] Implementation of PID for controlling drone
- [x] Drone Hovering
- [x] Drone movement in a rectangle of (1x2 meter)

**Task-3**

- [ ] Drone Swarming in a rectangle of(1x2 meter)  

## Installation Using pip
```
pip install hp-da-pt19
```
For Linux distributions with both python2 and python3 (e.g. Debian, Ubuntu, ...) you need to run

```
pip3 install hp-da-pt19
```

## Installation in Developer Mode
Using the commands below you can install the repository in developer mode. This allows you to modify the library and use the modified version regularly.

```
git clone https://github.com/da-vidi21/HP_DA_PT19.git
cd HP_DA_PT19
pip install -e .
```
## File Description
- **[calibration.py](hp_da_pt19/calibration.py)** :This file contains the code necessary for calibrating our camera. This step has several pre-requisites. we need to have a folder containing a set of checkerboard images taken using our camera. We have to make sure that these checkerboard images are of different poses and orientation. We need to provide the path to this directory and the size of the square in metres. We can also change the shape of the checkerboard pattern using the parameters given. We also have to make sure this matches with our checkerboard pattern. This code will generate two numpy files `calibration_matrix.npy` and `distortion_coefficients.npy`. These files are required to execute the next step that involves pose estimation. Note that the calibration and distortion numpy files given in this repository camera and its position specific.

    ```
    def calibrate(cam_src, frame_count, square_size, width, height, visualize=False):
    ```
    - Determines the camera matrix and the distortion coefficients from the image points.



- **[arucoTracking.py](hp_da_pt19/arucoTracking.py)** : This file contains the code that performs pose estimation and tracking after detecting the ArUCo markers. This is done in real-time for each frame obtained from the camera feed. We need to specify the path to the camera calibration matrix and distortion coefficients obtained from the previous step as well as the type for ArUCo marker we want to detect.
In our case we have used 4X4 ArUCo tag Dictionary.


    ```
    def start(self): 
    ```
    - Initiates the tracking thread.


    ```
    def read_position(self, id): 
    ```
    - Tracks the latest position of drone with given specific id and returns empty array if not detected.

    ```
    def read_smooth_position(self, id):
    ```
    - Returns the position of drone after applying smoothing.

    ```
    def read_z_rotation(self, id):
    ```
    - Returns the z rotation of the tag.

    ```
    def stop(self): 
    ```
    - Terminates the tracking thread

    ```
    def set_origin(self, origin):  
    ```
    - Sets the given passed coordinate as the orgin of the coordinate system.

    ```
    def update(self): 
    ```
    - For updating the position of the drone with the tracking data freceived from the latest frame.

    ```
    def generate_smooth_position(self, id):
    ```
    - For calculating the smoothed position of the drone.

    ```
    def moving_average(self, position_store):
    ```
    -Applies moving average to smooth out the coordinate values.

    ```
    def median_smoothing(self, position_store):
    ```
    - Applies median smoothing to smooth out the coordinate values.

    ```
    def get_velocity(self, id=0):
    ```
    - Returns the velocity of drone.


- **[Communication.py](hp_da_pt19/Communication.py)** : This file conatins the code that defines the drone's communication methods and the functions for controlling the drone movements and it's connection.This is done in real-time in a seperate thread.

    ```
    def __init__(self, IP_ADDRESS, PORT, id, debug=False):
    ```
    - Initializes the `Drone` object and sets base properties and values for it whenever the `Drone` object is created.

    ```
    def connect(self):
    ```
    - Starts the thread for `__com_thread `function.

    ```
    def disconnect(self):
    ```
    - Terminates the socket connection with the drone.

    ```
    def __com_thread(self):
    ```
    - Tries to establish a connection with the drone and launch the `__update()` function.

    ```
    def __update(self): 
    ```
    - Every 22nd millisecond delivers the most recent values of `rc_raw_data` and `set_cmd_data` to the drone.

    ```
    def __update_checksum(self):
    ```
    - Updates the checksum.

    ```
    def __get_LSB_MSB(self, val):
    ```
    - Returns the LSB and MSB values of the given input in range from 900(min) to 2100(max).

    ```
    def __set_rc_raw(self):
    ```
    - Sends the raw RC values like throttle, pitch, roll,etc to the drone.

    ```
    def set_cmd(self):
    ```
    - Sends commands to the drone for Take-off and Landing.

    ```
    def arm(self):
    ```
    - Modifies `rc_raw_data` in order to arm the drone.

    ```
    def disarm(self):
    ```
    - Modifies `rc_raw_data` in order to disarm the drone.

    ```
    def takeoff(self):
    ```
    - Modifies `rc_raw_data` in order to Take-off the drone.

    ```
    def land(self):
    ```
    -  Modifies `rc_raw_data` in order to land the drone.

    ```
    def set_throttle(self, val): 
    ```
    - Modifies `rc_raw_data` in order to change the throttle of the drone as per the input.

    ```
    def set_pitch(self, val): 
    ```
    - Modifies `rc_raw_data` in order to change the pitch of the drone as per the input.

    ```
    def set_roll(self, val): 
    ```
    - Modifies `rc_raw_data` in order to change the roll of the drone as per the input.

    ```
    def set_yaw(self, val):
    ```
    - Modifies `rc_raw_data` in order to change the yaw of the drone as per the input.

    ```
    def set_state(self, throttle, pitch, roll, yaw=1500):
    ```
    - Modifies the state values of the drone as per input and further modifies the `rc_raw_data` accordingly.

- **[pidaxischanged.py](hp_da_pt19/pidaxischanged.py)** :


