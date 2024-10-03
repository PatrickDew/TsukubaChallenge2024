# Tsukuba Challenge 2024 - LiDAR with FAST-LIO2 SLAM

This repository contains the methodology for setting up and running LiDAR (Livox-mid360) with FAST-LIO2 SLAM for the Tsukuba Challenge 2024. We will use ROS2 (Humble), along with packages for Livox SDK, drivers, and Fast-LIO2 SLAM.

## Table of Contents
1. [System Requirements](#system-requirements)
2. [Installation](#installation)
    - [1. Install ROS2 Humble](#install-ros2-humble)
    - [2. Install Livox SDK](#install-livox-sdk)
    - [3. Install Livox ROS Driver](#install-livox-ros-driver)
    - [4. Install FAST-LIO2](#install-fast-lio2)
3. [Running FAST-LIO2 SLAM](#running-fast-lio2-slam)
4. [Troubleshooting](#troubleshooting)

---

## System Requirements
- **PC**: Ubuntu 22.04 LTS
- **ROS2**: Humble
- **LiDAR**: Livox-mid360
- **Packages**:
  - Livox SDK
  - Livox ROS Driver
  - FAST-LIO2
- **Visualization**: Rviz/Gazebo
- **Optional Sensors**: Wheel encoders, IMU, or camera (for visual odometry)

---

## Installation

### 1. Install ROS2 Humble
Follow the [official ROS2 Humble installation guide](https://docs.ros.org/en/humble/Installation.html) for Ubuntu 22.04.

Once installed, source the ROS2 Humble environment:

```bash
source /opt/ros/humble/setup.bash
 ```

### 2. Install Livox SDK
1. Clone the Livox SDK repository and build the SDK:

```bash
git clone https://github.com/Livox-SDK/Livox-SDK2.git
cd Livox-SDK2
mkdir build && cd build
cmake ..
make
 ```

2. Run the Livox Lidar quick start to confirm installation:

```bash
./samples/livox_lidar_quick_start ../../../samples/livox_lidar_quick_start/mid360_config.json
 ```

### 3. Install Livox ROS Driver

1. Clone the Livox ROS Driver repository:
  ```bash
mkdir -p ~/livox_ws/src
cd ~/livox_ws/src
git clone https://github.com/Livox-SDK/livox_ros_driver2.git
 ```

2. Build the Livox ROS driver for ROS2 Humble:

  ```bash
cd ~/livox_ws/src/livox_ros_driver2
./build.sh humble
 ```

If you encounter the following error:

  ```bash
CMake Error at CMakeLists.txt:249 (find_library): 
Could not find LIVOX_LIDAR_SDK_LIBRARY...
 ```

You can fix it by updating the 'CMakeLists.txt':
  ```bash
find_library(LIVOX_LIDAR_SDK_LIBRARY NAME liblivox_lidar_sdk_shared.so PATHS /home/your_username/Livox-SDK2/build/sdk_core REQUIRED)
find_path(LIVOX_LIDAR_SDK_INCLUDE_DIR NAMES "livox_lidar_api.h" "livox_lidar_def.h" PATHS /home/your_username/Livox-SDK2/include REQUIRED)
 ```

3. Launch the Livox driver with Rviz:
```bash
ros2 launch livox_ros_driver2 rviz_MID360_launch.py
 ```

### 4. Install FAST-LIO2
1. Clone and build FAST-LIO2:
```bash
cd ~/ros2_humble/src
git clone https://github.com/hku-mars/FAST_LIO_SLAM_ros2.git
cd ..
colcon build
 ```
2. Source the ROS2 workspace:
```bash
source ~/ros2_humble/install/setup.bash
 ```

## Running FAST-LIO2 SLAM
1. Start the Livox LiDAR:

  ```bash
./livox_lidar_quick_start ../../../samples/livox_lidar_quick_start/mid360_config.json
 ```

2. Run the FAST-LIO2 SLAM node:

  ```bash
ros2 launch fast_lio mapping.launch.py config_file:=avia.yaml
 ```
## Troubleshooting

### 1. **Livox ROS Driver Build Error**
   **Error:** Could not find `LIVOX_LIDAR_SDK_LIBRARY`.

   **Solution:** Update the `CMakeLists.txt` to correctly point to the SDK installation paths as shown in the installation steps.

### 2. **FAST-LIO Shared Library Error**
   **Error:** `error while loading shared libraries: liblivox_ros_driver2__rosidl_typesupport_cpp.so`

   **Solution:** Run the following command to update the `LD_LIBRARY_PATH`:

   ```bash
   export LD_LIBRARY_PATH=/home/your_username/livox_ws/build/livox_ros_driver2:$LD_LIBRARY_PATH
```

### 3. **Wi-Fi Connection Setup**
   Ensure your Ethernet settings are configured as follows:

   - **IPv4 Address:** `192.168.1.50`
   - **Netmask:** `255.255.255.0`
   - **Gateway:** `192.168.1.1`

---

## References

- [FAST-LIO2 GitHub Repository](https://github.com/rohrschacht/FAST_LIO_SLAM_ros2)
- [Livox SDK GitHub Repository](https://github.com/Livox-SDK)
- [Livox ROS Driver GitHub Repository](https://github.com/Livox-SDK/livox_ros_driver2)
- [ROS2 Humble Documentation](https://docs.ros.org/en/humble/)

