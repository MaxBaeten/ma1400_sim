# :sparkles: MA1400 Simulator :sparkles:

The goal of this package is to provide a simulated Motoman MA1400 Welding Robot in Gazebo and simulated low level DX100 controller and expose them with an interface that is described by the motoman_driver ROS driver.

## Introduction

This package provides a simulated implementation of a 6-axis [MOTOMAN MA1400](http://www.motoman.co.uk/en/products/robots/product-view/?tx_catalogrobot_pi1%5Buid%5D=2508&cHash=d3ce061255ce9c779404fd7022030526) industrial robot arm, including a [URDF](http://wiki.ros.org/urdf), visual and collision meshes, and launch files to get it up and running in [Gazebo](http://gazebosim.org/).

Also included is an interface for controlling the trajectory of the robot that mimcs the interface used by [mototman_driver ROS driver](http://wiki.ros.org/motoman_driver). The low level trajectory following algorithm done by the actual robot controller \(such as the DX1000\) is also simulated.

## Basic URDF Creation

The [SolidWorks to URDF Exporter](http://wiki.ros.org/sw_urdf_exporter) made URDF creation much easier. After installing the plugin and opening the \*.stp filein SolidWorks \(2014 in my case\), a coordinate system must be defined at each joint and material properties of each link defined. By selecting `File->Export as URDF`, a wizard pops up which allows you to define each link in parent, child order, starting from the base_link.

At this point the plugin automatically generates the origins for each link and joint, as well as mass and inertia properties. It is up to the user to define the axis of rotation, as well as limits on rotation, velocity, and effort. The plugin also generates \*.stl files and links to them in the URDF. \(\*\*Note: I had the issue that the \*.stl files were saved with capitalized extension, which caused problems. After changing this, everything was fine.\)

I found that it was necessary to add a damping element to each joint. If this is left out, the robot is very springy in simulation.

### Using xacro files instead of urdf

At this point, the user must manually change some parts of the URDF. First, the user may want to use a \*.xacro file instead of a \*.urdf. These two files are very similar, except xacro files can use macros to generate repetitive blocks of text, making the file cleaner and less prone to mistakes. For more imformation, check out this [tutorial](http://wiki.ros.org/urdf/Tutorials/Using%20Xacro%20to%20Clean%20Up%20a%20URDF%20File).

### Additional gazebo specific elements

At this point, the simulated robot can be viewed inside rviz. However, a few more things must be defined before it is ready for simulation in Gazebo. This [tutorial](http://gazebosim.org/tutorials/?tut=ros_urdf) describes how to add specific Gazebo elements such as material (for color) and friction.

## Control

### Low level position control using ros_control

A low level position, velocity, or effort controller can be applied at each joint using the [ros_control](http://wiki.ros.org/ros_control) package. In order to do this several more things must be defined in the URDF or xacro file, including [transmissions](http://wiki.ros.org/urdf/XML/Transmission), and a Gazebo plugin. A /*.yaml file is also necessary to configure the controller and launch files are necessary to get everything started. Great tutorials on setting all of this up can be found [here](http://wiki.ros.org/urdf/Tutorials/Using%20a%20URDF%20in%20Gazebo) and [here](http://gazebosim.org/tutorials/?tut=ros_control).

### Trajectory controller simulator and interface

The [Motoplus-ROS Incremental Motion interface](http://wiki.ros.org/motoman_driver?action=AttachFile&do=get&target=MotoRos_EDS.pdf) defines the reaction of the controller in response to a `trajectory_msgs\JointTrajectory` message. `The moto_ros_interface` program subscribes to a `trajector_msgs\JointTrajectory` message and interpolates between the position and velocity targets to provide smooth motion with linear acceleration approximations.

## Run a demo

First boot up the gazebo simulation and MotoRos interface simulator
```
roslaunch ma1400_sim complete.gazebo.launch
```
To send a simple sine wave trajectory to all the joints of the welding arm, run the following script:
```
rosrun ma1400_sim trajectory_control.py
```
To rotate the positioner with a simple sine wave, run the following script:
```
rosrun ma1400_sim position_control.py
```
