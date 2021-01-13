# Moveo ROS2

## Summary

### Objectives

- porting moveo ros from jesse to ROS2.
- Discover ROS2 in the process (and MoveIt, by the way).

### Procedure

moveo ros is composed of three packages, that we are going to discover:

- moveo_urdf (urdf being the format used to modelize a robot in ros, it is propably containing it)
- moveo_moveit (No idea what it is)
- moveo_moveit_config (No Idea either)

## Porting moveo_urdf

moveo urdf is the only one responding to standard ros concepts, so I'll start here.

### Checking urdf : has the format changed ?

To check it, I started rviz2, added a RobotModel object, and selected model from file.
Loading seemed to work, but I miss transformations that are probably provided by a node or something that I didn't start. We'll come to that later.

Conclusion : the urdf format seems to be compliant with ros2.

### Updating package

#### Guessing the easiest way from existing folders

Opening package.xml or CMakeLists.txt show a lot of references to ros1 tools (roslaunch, catkin, ...), plus reference to Gazebo (which I'm not familiar with).

URDF and meshes folder should be up to date, the first because I checked with RVIZ, and the second because it is only stl ressources to display the robot.

launch folder has 4 files, three named from gazebo (So I'll will avoid them for now), one named display.launch. This one is probably my best entry point to understand something.

A fourth folder called config contains a yaml file with joint names listed - no idea what for.

#### The display.launch approach

Given My ROS level, the best idea I have is to follow the [ROS2 urdf tutorial](https://index.ros.org/doc/ros2/Tutorials/URDF/Using-URDF-with-Robot-State-Publisher/) tutorial, and try to adapt it to my robot.

in display.launch we see the following nodes are launched:

- rviz
- [robot_state_publisher](https://github.com/ros/robot_state_publisher/tree/foxy)
- [joint_state_publisher](https://index.ros.org/p/joint_state_publisher/)

Launching manually the three commands corresponding, and adding a RobotModel works, but does not show anything on the visualisation panel.

- `ros2 run robot_state_publisher robot_state_publisher /home/ben/dev/dev_moveo/src/moveo_ros/moveo_urdf/urdf/moveo_urdf.urdf`
- `ros2 run joint_state_publisher joint_state_publisher`
- `rviz2`

Let's go back to the tutorial, to see if one can do something. Following the tutorial, and replacing their robot by mine gave results.
*Note*: package:/ is found from the package name, using the environment -> rviz have to be started in the same environment for it to work.

##### Simplifying the tutorial to be closer from the original one

Looking at the original, one can see it is using the `joint_state_publisher` package, instead of the one we created during the tutorial (`state_publisher`), but their role is the same : telling everybody listening the state of our robot joint. The joint_state_publisher is still some kind of black magic to me, but I guess we'll understand more shortly.

## porting moveit packages

That is more complicated, because moveit is in the process of becoming moveit2, and as a consequence documentattion is not up to date, and some tools does not work ...
