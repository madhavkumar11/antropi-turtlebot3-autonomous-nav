# TurtleBot3 Autonomous Navigation using ROS 2 Jazzy

A complete implementation of **2D SLAM, map generation, robot localization, autonomous navigation, and obstacle avoidance** using a simulated TurtleBot3 Waffle Pi in Gazebo Harmonic.

This project was developed for the **Antropi Robotics Software Intern Assignment**.

The implementation uses ROS 2 Jazzy, Gazebo Harmonic, Cartographer for SLAM, and Navigation2 (Nav2) for autonomous navigation.

## 📽️ Demonstration

**Watch the Autonomous Navigation Demo:**

[▶ Watch the Demo Video](PASTE_YOUR_VIDEO_LINK_HERE)

> Replace `PASTE_YOUR_VIDEO_LINK_HERE` with your actual demonstration video URL.

## 🎯 Project Objectives

The primary objective is to demonstrate an autonomous mobile robot capable of mapping an indoor environment, estimating its position, and navigating toward a specified destination.

The project covers:

- **Simulation:** Run a TurtleBot3 Waffle Pi in a simulated house environment.
- **SLAM:** Generate a 2D occupancy grid map using Cartographer.
- **Teleoperation:** Manually control the robot to explore the environment.
- **Map Saving:** Save the generated occupancy grid map for reuse.
- **Localization:** Estimate the robot's position on a previously saved map.
- **Autonomous Navigation:** Send navigation goals through RViz.
- **Obstacle Avoidance:** Use Nav2's costmaps and local planner to navigate around detected obstacles.
- **Visualization:** Monitor laser scans, maps, robot poses, paths, and navigation goals in RViz.

## 🛠️ Technology Stack

| Component | Technology |
|---|---|
| Operating System | Ubuntu 24.04 LTS |
| Robotics Middleware | ROS 2 Jazzy |
| Simulation | Gazebo Harmonic |
| Robot Platform | TurtleBot3 Waffle Pi |
| SLAM | Cartographer |
| Navigation Framework | Navigation2 (Nav2) |
| Localization | AMCL |
| Visualization | RViz2 |
| Robot Control | TurtleBot3 Teleoperation |
| Map Format | YAML + PGM |
| Simulation Time | ROS 2 simulated clock |

## 🏗️ System Architecture

The project follows this workflow:

```text
                Ubuntu 24.04 LTS
                       |
                    ROS 2 Jazzy
                       |
          +------------+------------+
          |                         |
    Gazebo Harmonic              RViz2
          |                         |
    TurtleBot3 Waffle Pi       Visualization
          |
    LiDAR / LaserScan
          |
     SLAM Cartographer
          |
    Occupancy Grid Map
          |
     Map Saver (PGM/YAML)
          |
      Saved Map
          |
    Navigation2 (Nav2)
          |
       AMCL + TF
          |
     Global Costmap
          |
      Global Planner
          |
     Local Costmap
          |
      Local Controller
          |
    Velocity Commands
          |
     TurtleBot3 Motion
```

**Important:** Cartographer is used to create the map. During navigation, Nav2 uses AMCL to estimate the robot's position on the saved map. These are separate operating stages.

---

# 📋 Table of Contents

1. [Prerequisites](#-prerequisites)
2. [Install ROS 2 Jazzy](#1-install-ros-2-jazzy)
3. [Install Gazebo Harmonic](#2-install-gazebo-harmonic)
4. [Install TurtleBot3 Packages](#3-install-turtlebot3-packages)
5. [Install Cartographer](#4-install-cartographer)
6. [Install Navigation2](#5-install-navigation2)
7. [Configure the ROS Environment](#6-configure-the-ros-environment)
8. [Launch the Gazebo Simulation](#7-launch-the-gazebo-simulation)
9. [Run SLAM](#8-run-slam-and-create-the-map)
10. [Control the Robot](#9-control-the-robot-using-teleoperation)
11. [Save the Map](#10-save-the-generated-map)
12. [Launch Autonomous Navigation](#11-launch-navigation2)
13. [Localize the Robot](#12-localize-the-robot-using-rviz)
14. [Send a Navigation Goal](#13-send-a-navigation-goal)
15. [Verify ROS 2 Topics](#14-verify-ros-2-topics)
16. [Troubleshooting](#-troubleshooting)
17. [Repository Contents](#-repository-contents)
18. [Expected Results](#-expected-results)

---

# 📌 Prerequisites

Before starting, ensure the following are available:

- Ubuntu 24.04 LTS.
- ROS 2 Jazzy.
- Gazebo Harmonic.
- TurtleBot3 simulation packages.
- Cartographer and Cartographer ROS.
- Navigation2 and Nav2 Bringup.
- RViz2.
- A working graphical desktop environment.

**Recommended:** Use Ubuntu 24.04 with ROS 2 Jazzy installed directly, or a properly configured Ubuntu 24.04 virtual machine with adequate CPU and graphics resources.

## Check the Ubuntu Version

Open a terminal:

```bash
lsb_release -a
```

Check the ROS distribution:

```bash
echo $ROS_DISTRO
```

If ROS 2 Jazzy is installed and sourced, the output should include:

```text
jazzy
```

If the ROS distribution is not detected, source the installation:

```bash
source /opt/ros/jazzy/setup.bash
```

# 1. Install ROS 2 Jazzy

If ROS 2 Jazzy is already installed, skip this section.

For a new installation, follow the official ROS 2 Jazzy installation instructions:

https://docs.ros.org/en/jazzy/Installation/Ubuntu-Install-Debs.html

After installation, source ROS 2:

```bash
source /opt/ros/jazzy/setup.bash
```

Verify the installation:

```bash
ros2 --help
```

---

# 2. Install Gazebo Harmonic

Gazebo Harmonic is the simulation environment used to run the TurtleBot3 house world.

## Step 2.1: Update the package lists

```bash
sudo apt-get update
```

## Step 2.2: Install the required utilities

```bash
sudo apt-get install -y curl lsb-release gnupg
```

## Step 2.3: Add the Gazebo package signing key

```bash
sudo curl https://packages.osrfoundation.org/gazebo.gpg \
  --output /usr/share/keyrings/pkgs-osrf-archive-keyring.gpg
```

## Step 2.4: Add the Gazebo repository

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/pkgs-osrf-archive-keyring.gpg] http://packages.osrfoundation.org/gazebo/ubuntu-stable $(lsb_release -cs) main" \
  | sudo tee /etc/apt/sources.list.d/gazebo-stable.list > /dev/null
```

## Step 2.5: Update the package lists

```bash
sudo apt-get update
```

## Step 2.6: Install Gazebo Harmonic

```bash
sudo apt-get install -y gz-harmonic
```

## Step 2.7: Verify Gazebo

```bash
gz sim --version
```

You can also launch the Gazebo graphical application:

```bash
gz sim
```

Close the standalone simulation after verifying it.

> **Note:** The standalone Gazebo command is only for checking the installation. Later, the TurtleBot3 launch file will start the robot simulation.

---

# 3. Install TurtleBot3 Packages

Install the TurtleBot3 simulation, robot description, navigation, and teleoperation packages.

```bash
sudo apt update
```

```bash
sudo apt install -y \
  ros-jazzy-turtlebot3 \
  ros-jazzy-turtlebot3-gazebo \
  ros-jazzy-turtlebot3-teleop \
  ros-jazzy-turtlebot3-navigation2 \
  ros-jazzy-turtlebot3-cartographer
```

Install the ROS-Gazebo communication packages:

```bash
sudo apt install -y ros-jazzy-ros-gz
```

The ROS-Gazebo bridge enables communication between ROS 2 and Gazebo Transport when required by the simulation.

Verify the TurtleBot3 launch packages:

```bash
ros2 pkg list | grep turtlebot3
```

You should find packages related to TurtleBot3 simulation, teleoperation, Cartographer, and navigation.

---

# 4. Install Cartographer

Cartographer is used to build a 2D map from the robot's sensor data while the robot explores the environment.

Install Cartographer:

```bash
sudo apt install -y ros-jazzy-cartographer
```

Install Cartographer ROS:

```bash
sudo apt install -y ros-jazzy-cartographer-ros
```

Verify the installed packages:

```bash
ros2 pkg list | grep cartographer
```

The TurtleBot3 Cartographer launch file will be used to start SLAM and visualize the mapping process.

---

# 5. Install Navigation2

Navigation2 provides the navigation framework for localization, planning, obstacle avoidance, and robot control.

Install Navigation2:

```bash
sudo apt install -y ros-jazzy-navigation2
```

Install Nav2 Bringup:

```bash
sudo apt install -y ros-jazzy-nav2-bringup
```

Install the map server and map saver utilities:

```bash
sudo apt install -y ros-jazzy-nav2-map-server
```

Install AMCL:

```bash
sudo apt install -y ros-jazzy-nav2-amcl
```

Install the TurtleBot3 navigation dependencies:

```bash
sudo apt install -y \
  ros-jazzy-turtlebot3-navigation2 \
  ros-jazzy-turtlebot3-teleop
```

Verify the main packages:

```bash
ros2 pkg list | grep nav2
```

Check that the navigation launch file is available:

```bash
ros2 pkg prefix turtlebot3_navigation2
```

**Note:** The TurtleBot3 navigation launch files and parameters are supplied by the TurtleBot3 navigation package. Nav2 provides the underlying navigation servers and algorithms.

---

# 6. Configure the ROS Environment

Configure the ROS environment so new terminals automatically load ROS 2 Jazzy and use the appropriate TurtleBot3 settings.

## Step 6.1: Set the ROS domain ID

The following command sets the ROS domain ID to `30`:

```bash
echo 'export ROS_DOMAIN_ID=30' >> ~/.bashrc
```

## Step 6.2: Source ROS 2 Jazzy automatically

```bash
echo 'source /opt/ros/jazzy/setup.bash' >> ~/.bashrc
```

## Step 6.3: Set the TurtleBot3 model

This project uses the Waffle Pi model:

```bash
echo 'export TURTLEBOT3_MODEL=waffle_pi' >> ~/.bashrc
```

## Step 6.4: Reload the shell configuration

```bash
source ~/.bashrc
```

Verify the configuration:

```bash
echo $ROS_DISTRO
echo $ROS_DOMAIN_ID
echo $TURTLEBOT3_MODEL
```

Expected output:

```text
jazzy
30
waffle_pi
```

**Important:** Use the same `ROS_DOMAIN_ID` in every terminal involved in the simulation. If a terminal is not communicating with the others, check its domain ID and ROS environment.

---

# 7. Launch the Gazebo Simulation

The first stage is to launch the TurtleBot3 house world.

## Step 7.1: Open a terminal

Use:

```text
Ctrl + Alt + T
```

## Step 7.2: Configure the robot model

```bash
export TURTLEBOT3_MODEL=waffle_pi
```

## Step 7.3: Launch the TurtleBot3 house world

```bash
ros2 launch turtlebot3_gazebo turtlebot3_house.launch.py
```

This launch file starts the TurtleBot3 house simulation.

Depending on the installed package version and simulation configuration, the launch process starts the robot, the house environment, and the required simulation interfaces.

Keep this terminal open.

### Verify the simulation

Open another terminal:

```bash
ros2 topic list
```

Check the robot's laser scan topic:

```bash
ros2 topic echo /scan --once
```

If the topic exists and sensor data is being published, the robot's laser sensor is communicating with ROS 2.

> **Important:** Continue using `waffle_pi` throughout this project. If you select another model, such as `burger`, make sure that the simulation and all model-dependent configurations are compatible.

---

# 8. Run SLAM and Create the Map

In this stage, Cartographer processes sensor data and estimates the robot's movement while constructing a map of the house.

## Step 8.1: Open a second terminal

Keep the Gazebo terminal running.

Open another terminal:

```text
Ctrl + Alt + T
```

Set the robot model:

```bash
export TURTLEBOT3_MODEL=waffle_pi
```

## Step 8.2: Launch Cartographer

```bash
ros2 launch turtlebot3_cartographer cartographer.launch.py use_sim_time:=True
```

The launch process should start Cartographer and RViz2 using the TurtleBot3 configuration.

In RViz2, observe:

- Laser scan measurements.
- Robot position and orientation.
- The developing map.
- The robot's movement through the environment.

### Why use simulated time?

Gazebo runs a simulation clock. Setting:

```bash
use_sim_time:=True
```

allows the ROS nodes to use the simulation clock rather than the computer's wall clock.

This is important for synchronizing sensor messages, transforms, and SLAM processing.

### Important: Use Cartographer, not SLAM Toolbox, for this workflow

This README uses the TurtleBot3 Cartographer launch file.

Although SLAM Toolbox can also generate maps, it is a different SLAM implementation with its own launch files and configuration.

Do not launch both SLAM implementations simultaneously for the same mapping session unless you have deliberately configured separate data flows.

---

# 9. Control the Robot Using Teleoperation

The robot must explore the environment so that SLAM can build a sufficiently complete map.

## Step 9.1: Open a third terminal

Keep both Gazebo and Cartographer running.

Open a new terminal:

```text
Ctrl + Alt + T
```

Set the model:

```bash
export TURTLEBOT3_MODEL=waffle_pi
```

## Step 9.2: Start keyboard teleoperation

```bash
ros2 run turtlebot3_teleop teleop_keyboard
```

The terminal will display the keyboard controls.

Use the keys shown by the teleoperation program to move the robot.

Typical TurtleBot3 keyboard controls include:

| Key | Action |
|---|---|
| `w` | Move forward |
| `x` | Move backward |
| `a` | Rotate left |
| `d` | Rotate right |
| `s` | Stop |
| `q` | Increase linear/angular speed |
| `z` | Decrease linear/angular speed |

The exact controls and available speed adjustments depend on the installed teleoperation version. Follow the instructions printed in the terminal.

## Step 9.3: Explore the complete house

Move the robot slowly through the environment.

For good mapping results:

1. Drive through the accessible corridors.
2. Explore each room and major open area.
3. Turn the robot slowly near corners.
4. Avoid moving too quickly.
5. Revisit areas where the map appears incomplete.
6. Avoid abrupt movements that could degrade scan matching.

Observe the map in RViz2 as the robot moves.

Continue until the important accessible areas of the house have been mapped.

**Expected result:** RViz2 displays a 2D occupancy grid representing walls, free space, and unexplored regions.

---

# 10. Save the Generated Map

After mapping the environment, save the occupancy grid so it can be reused for autonomous navigation.

## Step 10.1: Open a new terminal

Keep Gazebo and Cartographer running while the map is being generated.

Open a new terminal:

```bash
Ctrl + Alt + T
```

## Step 10.2: Save the map

The following command saves the map in your home directory:

```bash
ros2 run nav2_map_server map_saver_cli -f ~/antropi_house_map
```

The command normally creates two files:

```text
~/antropi_house_map.yaml
~/antropi_house_map.pgm
```

The YAML file contains map metadata, including the image filename, resolution, origin, and occupancy thresholds.

The PGM file contains the occupancy grid image.

### Alternative: Save using the generic filename `map`

If you prefer to use the filename from the original workflow:

```bash
ros2 run nav2_map_server map_saver_cli -f ~/map
```

This creates:

```text
~/map.yaml
~/map.pgm
```

**Important:** Use the same filename when launching Nav2. The examples below use `antropi_house_map`; if you saved the map as `map`, substitute that filename in the navigation command.

## Step 10.3: Verify the saved files

```bash
ls -lh ~/antropi_house_map.yaml ~/antropi_house_map.pgm
```

Inspect the YAML configuration:

```bash
cat ~/antropi_house_map.yaml
```

The YAML file should reference the correct PGM filename.

For example, its metadata will contain entries similar to:

```yaml
image: map.pgm
resolution: 0.05
origin: [-5.0, -5.0, 0.0]
negate: 0
occupied_thresh: 0.65
free_thresh: 0.25
```

The resolution and origin shown above are illustrative. Use the actual values generated by your map saver.

## Step 10.4: Copy the map into your repository

If the map is to be included in your GitHub repository:

```bash
cp ~/antropi_house_map.yaml .
cp ~/antropi_house_map.pgm .
```

Run these commands from the root of your repository.

Verify:

```bash
ls -lh antropi_house_map.yaml antropi_house_map.pgm
```

---

# 11. Launch Navigation2

Once the map has been saved, switch from the mapping stage to autonomous navigation.

**Important:** Do not start a second Gazebo simulation while the original simulation is running. The original Gazebo world can remain active; the SLAM and teleoperation nodes should be stopped before starting the navigation workflow.

## Step 11.1: Stop Cartographer

In the Cartographer terminal, press:

```text
Ctrl + C
```

## Step 11.2: Stop teleoperation

In the teleoperation terminal, press:

```text
Ctrl + C
```

## Step 11.3: Keep Gazebo running

Leave the Gazebo simulation running.

You should now have the simulation active without the mapping and teleoperation processes.

## Step 11.4: Open a new terminal

Set the TurtleBot3 model:

```bash
export TURTLEBOT3_MODEL=waffle_pi
```

Make sure ROS 2 is sourced:

```bash
source /opt/ros/jazzy/setup.bash
```

## Step 11.5: Launch Nav2 with the saved map

For the `antropi_house_map` filename:

```bash
ros2 launch turtlebot3_navigation2 navigation2.launch.py \
  use_sim_time:=True \
  map:=$HOME/antropi_house_map.yaml
```

If you saved the map as `map.yaml`, use:

```bash
ros2 launch turtlebot3_navigation2 navigation2.launch.py \
  use_sim_time:=True \
  map:=$HOME/map.yaml
```

This launch file starts the TurtleBot3 navigation configuration with the saved map.

Depending on the installed package version, the launch configuration starts the required Nav2 servers and RViz2.

The navigation system typically includes:

- Map server.
- AMCL localization.
- Planner server.
- Controller server.
- Behavior server.
- BT Navigator.
- Lifecycle management.
- Global and local costmaps.

The exact set of nodes depends on the launch configuration supplied by the installed package.

### What happens during navigation?

Nav2 uses the saved occupancy grid as its global map.

AMCL estimates the robot's position using laser scan data and the map.

The global planner calculates a route to the destination, while the local controller generates velocity commands to follow that route and respond to nearby obstacles.

---

# 12. Localize the Robot Using RViz2

After Nav2 starts, the robot must be localized on the saved map.

## Step 12.1: Locate the robot in RViz2

Wait for the map to load.

You should see the occupancy grid corresponding to the previously generated map.

## Step 12.2: Select `2D Pose Estimate`

In the RViz2 toolbar, select:

```text
2D Pose Estimate
```

Click the approximate position of the robot on the map.

Drag the mouse in the direction the robot is facing, then release.

This provides an initial pose estimate for AMCL.

## Step 12.3: Allow AMCL to converge

Observe the laser scan overlay and the robot's estimated position.

Move the robot a small amount if needed, using an appropriate control method, and allow AMCL to refine the pose estimate.

A successful localization should show the laser scan measurements aligning reasonably well with the walls and other mapped structures.

### If localization is incorrect

- Check that the initial pose is close to the actual robot position.
- Confirm that the correct map is loaded.
- Check that the laser scan is being published.
- Verify that simulated time is enabled.
- Inspect the TF tree for missing transforms.
- Make sure the simulation and Nav2 are using compatible robot configurations.

Do not send a navigation goal until the localization estimate is reasonably accurate.

---

# 13. Send a Navigation Goal

After localization, use Nav2 to navigate the robot autonomously.

## Step 13.1: Select `Nav2 Goal`

In the RViz2 toolbar, select the navigation goal tool.

Depending on the RViz2 configuration, it may be displayed as:

```text
Nav2 Goal
```

## Step 13.2: Choose a destination

Click the desired destination on the map.

Drag in the direction the robot should face, then release the mouse button.

Nav2 will attempt to plan and execute a route from the robot's current estimated position to the selected goal.

## Step 13.3: Observe autonomous navigation

Monitor the following in RViz2:

- Global map.
- Robot pose.
- Laser scan.
- Global path.
- Local costmap.
- Global costmap.
- Navigation goal.
- Robot movement.

If the route is valid and the system is correctly configured, the robot should move toward the destination.

## Step 13.4: Test obstacle avoidance

If the simulation contains suitable obstacles, observe how the robot responds when an obstacle lies near its planned path.

Nav2 can use its costmaps and local controller to adjust the robot's motion around detected obstacles.

**Important:** Successful obstacle avoidance depends on the sensor configuration, costmap parameters, controller configuration, obstacle placement, and available free space. It should be verified during the demonstration rather than assumed from a successful launch alone.

---

# 14. Verify ROS 2 Topics

The following commands help verify communication between Gazebo, the robot, and Nav2.

Open another terminal.

Source ROS 2:

```bash
source /opt/ros/jazzy/setup.bash
```

Set the robot model:

```bash
export TURTLEBOT3_MODEL=waffle_pi
```

## Step 14.1: List active topics

```bash
ros2 topic list
```

## Step 14.2: Check laser scan data

```bash
ros2 topic echo /scan --once
```

This should display a laser scan message if the topic is active and publishing.

## Step 14.3: Check odometry

```bash
ros2 topic echo /odom --once
```

This should display odometry information if the robot publishes its odometry on `/odom`.

## Step 14.4: Check velocity commands

```bash
ros2 topic list | grep cmd_vel
```

Depending on the navigation configuration, velocity commands may pass through a velocity smoother or another intermediate topic before reaching the robot.

Inspect the available topics rather than assuming a specific topic name.

## Step 14.5: Inspect active nodes

```bash
ros2 node list
```

Look for the expected navigation components, such as AMCL, the planner server, the controller server, and the BT Navigator.

The exact node names may vary with the package version.

## Step 14.6: Check transforms

```bash
ros2 run tf2_tools view_frames
```

This generates a TF frame diagram for inspection.

For a typical TurtleBot3 navigation setup, important frames include:

```text
map
odom
base_footprint
base_link
```

The robot's laser frame should also be connected to the robot's base through the appropriate transform chain.

The exact frame names depend on the robot description.

---

# 🔧 Troubleshooting

## 1. Gazebo closes automatically

**Possible causes:**

- Graphics driver or rendering problems.
- Insufficient memory or CPU resources.
- Missing or incompatible simulation dependencies.
- A crash in the Gazebo process.

**Troubleshooting steps:**

Check the installed Gazebo version:

```bash
gz sim --version
```

Check memory availability:

```bash
free -h
```

Inspect running processes:

```bash
ps aux | grep -E 'gz sim|gzserver|gzclient'
```

Run the simulation from a terminal so error messages remain visible:

```bash
ros2 launch turtlebot3_gazebo turtlebot3_house.launch.py
```

If Gazebo crashes, examine the terminal output before attempting further changes.

Avoid launching multiple copies of the same simulation.

## 2. RViz2 does not display the map

Check the available topics:

```bash
ros2 topic list
```

Inspect the map topic:

```bash
ros2 topic echo /map --once
```

If `/map` is not available, verify that the map server or SLAM node is running as appropriate for the current stage.

During mapping, Cartographer should be producing mapping data. During navigation, Nav2 should load the saved map.

Also verify the RViz2 Fixed Frame setting against the available TF frames.

## 3. Laser scan is not visible

Check the laser topic:

```bash
ros2 topic list | grep scan
```

Inspect the data:

```bash
ros2 topic echo /scan --once
```

If the topic exists but no data arrives, inspect the simulation and ROS-Gazebo interfaces.

In RViz2, verify that the LaserScan display uses the correct topic and that the Fixed Frame is valid.

## 4. Nav2 fails to start

Verify that the map files exist:

```bash
ls -lh ~/antropi_house_map.yaml ~/antropi_house_map.pgm
```

Check that the YAML file points to the correct image.

Verify the package installation:

```bash
ros2 pkg prefix turtlebot3_navigation2
```

Check the launch file's available arguments:

```bash
ros2 launch turtlebot3_navigation2 navigation2.launch.py --show-args
```

Inspect the terminal output for missing parameters, invalid map paths, lifecycle failures, or TF errors.

## 5. Robot does not move toward the goal

Check localization first.

Then inspect the navigation nodes:

```bash
ros2 node list
```

Check the available velocity command topics:

```bash
ros2 topic list | grep cmd_vel
```

Inspect TF frames:

```bash
ros2 run tf2_tools view_frames
```

Common causes include:

- Incorrect initial pose.
- Invalid or unavailable TF transforms.
- No valid path to the goal.
- Obstacles blocking the route.
- Inactive Nav2 lifecycle nodes.
- A mismatch between the robot model and navigation parameters.
- Incorrect simulated-time configuration.

## 6. Robot is localized incorrectly

Use `2D Pose Estimate` in RViz2 to provide a better initial pose.

Confirm that:

- The correct map is loaded.
- The robot is in the corresponding location in Gazebo.
- Laser scan measurements align with the map.
- The map-to-odom and odom-to-base transform chain is available.

Allow AMCL to converge before sending a goal.

## 7. ROS 2 nodes cannot communicate

Check the ROS distribution:

```bash
echo $ROS_DISTRO
```

Check the domain ID:

```bash
echo $ROS_DOMAIN_ID
```

Source ROS 2:

```bash
source /opt/ros/jazzy/setup.bash
```

Ensure all relevant terminals use the same `ROS_DOMAIN_ID`.

If the problem persists, inspect the ROS middleware configuration and the network settings of your machine or virtual machine.

---

# 📂 Repository Contents

The repository should contain the following files:

```text
.
├── README.md
├── map.yaml
├── map.pgm

```

## Map Files

### `map.yaml`

Contains the map metadata required by the ROS 2 map server.

### `map.pgm`

Contains the occupancy grid image generated from the SLAM session.

### `README.md`

Documents the installation process, environment configuration, mapping workflow, map saving, and autonomous navigation procedure.

---

# ✅ Expected Results

After completing the workflow, the project should demonstrate:

1. Successful launch of the TurtleBot3 house world in Gazebo Harmonic.
2. Successful acquisition of laser scan data.
3. Live 2D mapping using Cartographer.
4. Manual robot control through keyboard teleoperation.
5. Successful saving of the occupancy grid as PGM and YAML files.
6. Successful loading of the saved map into Nav2.
7. Robot localization using AMCL and the `2D Pose Estimate` tool.
8. Autonomous navigation toward a selected RViz2 goal.
9. Obstacle avoidance under suitable simulation conditions.

These outcomes should be verified during execution and documented in the demonstration video.

---

# 🚀 Future Improvements

Possible extensions include:

- Comparing Cartographer and SLAM Toolbox.
- Tuning Nav2 global and local costmaps.
- Optimizing planner and controller parameters.
- Testing navigation in different indoor environments.
- Evaluating localization accuracy.
- Measuring navigation completion time and path length.
- Implementing waypoint navigation.
- Adding autonomous exploration.
- Testing recovery behaviors when navigation fails.

---

# 📚 References

- ROS 2 Jazzy Documentation: https://docs.ros.org/en/jazzy/
- Nav2 Documentation: https://docs.nav2.org/
- Gazebo Documentation: https://gazebosim.org/docs/
- TurtleBot3 e-Manual: https://emanual.robotis.com/docs/en/platform/turtlebot3/overview/
- ROBOTIS TurtleBot3 Simulation Repository: https://github.com/ROBOTIS-GIT/turtlebot3_simulations
- ROBOTIS TurtleBot3 Repository: https://github.com/ROBOTIS-GIT/turtlebot3
- Cartographer ROS Repository: https://github.com/ros2/cartographer_ros

---

## 👨‍💻 Project Information

**Project:** TurtleBot3 Autonomous Navigation using ROS 2 Jazzy

**Assignment:** Antropi Robotics Software Intern Assignment

**Platform:** TurtleBot3 Waffle Pi

**Simulation:** Gazebo Harmonic

**Mapping:** Cartographer

**Localization:** AMCL

**Navigation:** Navigation2

**Visualization:** RViz2

**Operating System:** Ubuntu 24.04 LTS

---

⭐ If this project is useful, consider starring the repository.
