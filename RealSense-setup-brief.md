# RealSense Setup Under Ubuntu 22.04 + ROS2 Humble

## Step 1: librealsense installation

### Option 1: Install from Debian packages

1.  Download the packages below from https://github.com/mengyui/librealsense2-dkms/releases/tag/initial-support-for-kernel-5.15
```
librealsense2-dkms-dkms_1.3.14_amd64.deb
PREBUILT-DEB-PACKAGE-librealsense2_2.50.0-0.realsense0.6128_amd64.zip
libssl1.1_1.1.1f-1ubuntu2.16_amd64.deb
```

2.  Update and install the dependency packages
```bash
sudo apt-get update
sudo apt-get install dkms git libusb-1.0-0-dev libudev-dev libglfw3-dev pkg-config libgtk-3-dev at
```

3. Install the downloaded DKMS Debian package to activate patched kernel modules
```bash
sudo dpkg -i librealsense2-dkms-dkms_1.3.14_amd64.deb
```

4. Install libssl1.1
```bash
sudo dpkg -i libssl1.1_1.1.1f-1ubuntu2.16_amd64.deb
```

5. Unzip PREBUILT-DEB-PACKAGE-librealsense2_2.50.0-0.realsense0.6128_amd64.zip and install the Debian packages
```bash
sudo dpkg -i librealsense2-udev-rules_2.50.0-0~realsense0.6128_amd64.deb
sudo dpkg -i librealsense2_2.50.0-0~realsense0.6128_amd64.deb
sudo dpkg -i librealsense2-gl_2.50.0-0~realsense0.6128_amd64.deb
sudo dpkg -i librealsense2-net_2.50.0-0~realsense0.6128_amd64.deb
sudo dpkg -i librealsense2-utils_2.50.0-0~realsense0.6128_amd64.deb
sudo dpkg -i librealsense2-dev_2.50.0-0~realsense0.6128_amd64.deb
```
### Option 2: Install from source

1. Make Ubuntu Up-to-date: update Ubuntu distribution, including getting the latest stable kernel
```bash
sudo apt-get update && sudo apt-get upgrade && sudo apt-get dist-upgrade
```

2.	Get librealsense sources in one of the following ways:
- Download the complete source tree with git
```bash
git clone https://github.com/IntelRealSense/librealsense.git
```
- Download and unzip the latest stable version from master branch: https://github.com/IntelRealSense/librealsense/archive/master.zip

3.	Unplug any connected Intel RealSense camera.

4.	Install the core packages required to build librealsense binaries and the affected kernel modules:
```bash
sudo apt-get install git libssl-dev libusb-1.0-0-dev libudev-dev pkg-config libgtk-3-dev
sudo apt-get install libglfw3-dev libgl1-mesa-dev libglu1-mesa-dev at 
```

5.	Run Intel RealSense permissions script from librealsense root directory:
```bash
./scripts/setup_udev_rules.sh
```

6.	Building librealsense2 SDK
- Navigate to librealsense root directory and run mkdir build && cd build
- Run CMake:
```bash
cmake ../ # The default build is set to produce the core shared object and unit-tests binaries in Debug mode.
# Note: If the host system need CPU optimization, cmake option as below is suggested.
cmake ../ -DCMAKE_BUILD_TYPE=Release # Use -DCMAKE_BUILD_TYPE=Release to build with optimizations.
cmake ../ -DBUILD_EXAMPLES=true # Builds librealsense along with the demos and tutorials
cmake ../ -DBUILD_EXAMPLES=true -DBUILD_GRAPHICAL_EXAMPLES=false # For systems without OpenGL or X11 build only textual examples
```
- Recompile and install librealsense binaries:
```bash
sudo make uninstall && make clean && make && sudo make install
```

7.	Apply patched kernel modules
- Download DKMS Debian package from https://github.com/mengyui/librealsense2-dkms/releases/download/initial-support-for-kernel-5.15/librealsense2-dkms-dkms_1.3.14_amd64.deb 
- Install the downloaded DKMS Debian package to activate patched kernel modules
```
sudo dpkg -i librealsense2-dkms-dkms_1.3.14_amd64.deb
```
Note: For more details about the steps, please refer to https://github.com/IntelRealSense/librealsense/blob/master/doc/installation.md  for the case of Ubuntu18.

## Step 2: Install ROS2 wrapper

1. Install ROS2 Humble according to https://docs.ros.org/en/humble/Installation.html

2. Install RealSense™ ROS2 wrapper from sources(https://github.com/IntelRealSense/realsense-ros/tree/ros2-beta#step-3-install-intel-realsense-ros2-wrapper-from-sources )
- Create a ROS2 workspace
```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws/src/
```
- Clone the latest ROS2 Intel® RealSense™ wrapper into '~/ros2_ws/src/'
```bash
git clone https://github.com/IntelRealSense/realsense-ros.git -b ros2-beta
cd ~/ros2_ws
```

3.	Install dependencies(https://github.com/IntelRealSense/realsense-ros/tree/ros2-beta#step-4-install-dependencies) 
```bash
sudo apt-get install python3-rosdep -y
sudo rosdep init 
rosdep update
rosdep install -i --from-path src --rosdistro humble --skip-keys=librealsense2 -y
```

4.	Install colcon build tool
```bash
sudo apt install python3-colcon-common-extensions
```

5.	Build(https://github.com/IntelRealSense/realsense-ros/tree/ros2-beta#step-5-build) 
```bash
colcon build
```

6.	Terminal environment(https://github.com/IntelRealSense/realsense-ros/tree/ros2-beta#step-6-terminal-environment)
```bash
source /opt/ros/humble/setup.bash
cd ~/ros2_ws
source install/local_setup.bash
```

7.	ROS2 Wrapper usage instruction: https://github.com/IntelRealSense/realsense-ros/tree/ros2-beta#usage-instructions 
