# Brief

This repository is to integerate  ADAS active cruise with joy parser in ROS2.

# How to Install
Clone this repository in ROS2 workspace along with following packages.

  - Active_trim
  - adas_boat_system
  - adas_joy_parser
  - navatics_adas_base
  - navatics_adas_usv
  - pos_heading_hold

Clone hk_adas_boat_gazebo and  vrx package in VRX workspace.

Configure VRX , ROS2 and ROS2-1 bridge environments with the following tutorial.

[Gazebo-ROS 2-1 Bridge](https://epropulsion.feishu.cn/docs/doccnaB0AtFvZRiCJvqmCrgRaef)


# How to Use

  Configure VRX environments , enter the following command to launch speedboat :
```
roslaunch hk_adas_boat_gazebo speedboat_vrx.launch
```
Configure two ROS2 environment to launch navatics adas usv and joy parser package:  

```
ros2 launch navatics_adas_usv adas.launch.py
```

```
ros2 launch adas_joy_parser adas_joy_parser.launch.py 
```

Press the 'LB' button on joystick for free mode and the 'Y' button for active cruise mode
Once active cruise mode is on , key metrics like normal error (ye), yaw error , action taken , and reward  at that instance can we observed from the terminal .

To observe refrence path and current path with respect to each other on matplot plot , enter follwowing command in VRX environment:

```
rosrun hk_adas_boat_gazebo active_cruise_test.py
```

```
/thrusters/left_thrust_angle
/thrusters/left_thrust_cmd
/thrusters/right_thrust_angle
/thrusters/right_thrust_cmd

#command to ADAS funciton
controller/cmd_vel
controller/speed_incre
```
ROS services:
```
controller/mode_switch
```

Command rate : 10 Hz

launch/joy_command.launch: edit param "autorepeat_rate" to adjust rate of last updated state broadcast in the absence of input.

# Driving Mode

## Currently there are 3 driving mode in this package:
1. Mode 1: FREE
2. Mode 2: WHEEL_AND_THROTTLE
3. Mode 3: ADAS_POS_HOLD
4. Mode 4: ADAS_HEADING_HOLD
4. Mode 5: ADAS_SPEED_HOLD
## The F710 controller
F710 is used for our adas_joy_parser development. Before running the node, please ensure the that:

    1. The XID/HID switch is switched to the XID for using the Xinput format.
    2. The LED besides mode button is turned off.

The following are the buttons on the F710 controller.

<p float="left">
  <img src="./images/F710.jpg" width="650" />
  <img src="./images/F710_top.jpg" width="450" /> 
</p>

## To change mode press the following buttons:
1. Press 'X' switch to Mode 1
2. Press 'Y' switch to Mode 2
3. Press 'B' switch to Mode 3
4. Press 'A' switch to Mode 4
4. Press 'RT' switch to Mode 5

## Mode Descriptions:
1. **Mode 1 (FREE) Manual Driving: **

This mode maps directly the left/right joysticks to the left/right motor module so that driver can command each actuator directly.

    Left/right Joystick "vertical" axis: motor throttle

    Left/right Joystick "horizontal" axis: motor steering

2. **Mode 2 (WHEEL_AND_THROTTLE) Wheel and Throttle Driving:**

This mode coupled both steering and throttle between two motor module so that driver controls only the throtle and direction of the boat.
 
    Left Joystick "vertical" axis: motor throttle

    Right Joystick "horizontal" axis: motor steering

3. **Mode 3 & 4 (ADAS_POS_HOLD & ADAS_HEADING_HOLD) Assisted Driving:**

This mode will turn on the PID control to maintain orientation and position, so when this mode is turned on the boat will start to keep the position and orientation in place.
The Joystick will then publishes force cmd in body frame to the assisted control node. The position reference will be updated in accordance to the command given.

**Reference repository**: https://code.navatics.dev/adas/ros2/library/core_planning/pid_controller/pos_heading_hold

    left joystick "vertical" axis : X command and update x reference state

    left joystick "horizontal" axis : Y command and update y reference state

    right joystick left/right : W command and update w reference state

    start button : reset controller gains

4. **Mode 4 (ADAS_SPEED_HOLD) Assisted Driving:**

This mode will turn on the PID control to maintain the current surge speed of the boat. Driver can change the desired speed to be maintained. However the steering will remain manual so it is sent directly to the motor steering.
The Joystick will then publishes speed increment cmd to the assisted control node. The speed reference will be updated in accordance to the command given.

**Reference repository**: https://code.navatics.dev/adas/ros2/library/core_planning/pid_controller/speed_hold

    "DPAD UP/DOWN" switch : Increment/Decrement of 0.1 to the reference speed
     
    "RTrigger" switch : Increment of the 0.5 to the reference speed
    
    "LTrigger" switch : Increment of 1.0 to the reference speed

    Right Joystick "horizontal" axis : motor steering

    Start button : reset controller gains
