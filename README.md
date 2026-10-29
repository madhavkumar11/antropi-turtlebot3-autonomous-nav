# antropi-turtlebot3-autonomous-nav
Autonomous navigation &amp; SLAM mapping using TurtleBot3 (Waffle Pi) on ROS 2 Jazzy and Gazebo Harmonic.
# TurtleBot3 Autonomous Navigation (ROS 2 Jazzy)

This repository contains the mapping and navigation implementation for the **Antropi Robotics Software Intern Assignment**. It demonstrates 2D SLAM, AMCL localization, and autonomous navigation with dynamic obstacle avoidance using a simulated TurtleBot3 Waffle Pi in Gazebo Harmonic.

## 📽️ Demonstration
**[Watch the Autonomous Navigation Demo Video Here](PASTE_YOUR_VIDEO_LINK_HERE)**

## 📂 Repository Contents
* `antropi_house_map.yaml`: Nav2 map configuration parameters.
* `antropi_house_map.pgm`: 2D occupancy grid generated via SLAM Toolbox.
* `README.md`: Environment setup and reproduction instructions.

---

## ⚙️ Environment Setup & Dependencies

This implementation utilizes the official ROBOTIS ROS 2 Jazzy binary packages. 

### 1. Install ROS 2 Packages
Ensure your Ubuntu 24.04 system has the required ROS 2 Jazzy navigation and simulation stacks installed:
```bash
sudo apt update
sudo apt install -y \
  ros-jazzy-navigation2 \
  ros-jazzy-nav2-bringup \
  ros-jazzy-turtlebot3-gazebo \
  ros-jazzy-turtlebot3-teleop \
  ros-jazzy-turtlebot3-navigation2 \
  ros-jazzy-slam-toolbox \
  ros-jazzy-ros-gz-bridge
