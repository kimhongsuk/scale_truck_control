# Scale Truck Control

# Hardware
>Nvidia Jetson Xavier  
>USB Camera (Camera)   
>RPLidar A3 (Lidar)  
>OpenCR 1.0 (ARM)  

# 0. Install Environment
>## 0.1 Jetpack 4.5.1 (ubuntu 18.04 LTS)
>https://developer.nvidia.com/embedded/jetpack

>## 0.2.1 OpenCV 4.4.0
>~~~
>sudo apt-get purge  libopencv* python-opencv
>sudo apt-get autoremove
>sudo find /usr/local/ -name "*opencv*" -exec rm -i {} \;
>~~~
>
>~~~
>sudo apt-get update
>sudo apt-get upgrade
>
>sudo apt-get -y install build-essential cmake
>sudo apt-get -y install pkg-config
>sudo apt-get -y install libjpeg-dev libtiff5-dev libpng-dev
>sudo apt-get -y install ffmpeg libavcodec-dev libavformat-dev libswscale-dev libxvidcore-dev libx264-dev libxine2-dev
>sudo apt-get -y install libv4l-dev v4l-utils
>sudo apt-get -y install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev 
>sudo apt-get -y install libgtk-3-dev
>sudo apt-get -y install mesa-utils libgl1-mesa-dri libgtkgl2.0-dev libgtkglext1-dev
>sudo apt-get -y install libatlas-base-dev gfortran libeigen3-dev
>sudo apt-get -y install python3-dev python3-numpy
>~~~
>
>~~~
>mkdir OpenCV && cd OpenCV
>git clone -b 4.4.0 https://github.com/opencv/opencv
>git clone -b 4.4.0 https://github.com/opencv/opencv_contrib
>cd opencv && mkdir build && cd build
>~~~
>
>~~~
>cmake -D CMAKE_BUILD_TYPE=RELEASE \
>-D CMAKE_INSTALL_PREFIX=/usr/local \
>-D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules \
>-D WITH_OPENCL=OFF \
>-D WITH_CUDA=ON \
>-D CUDA_ARCH_BIN=7.2 \
>-D CUDA_ARCH_PTX="" \
>-D WITH_CUDNN=ON \
>-D WITH_CUBLAS=ON \
>-D ENABLE_FAST_MATH=ON \
>-D CUDA_FAST_MATH=ON \
>-D OPENCV_DNN_CUDA=ON \
>-D ENABLE_NEON=ON \
>-D WITH_QT=OFF \
>-D WITH_OPENMP=ON \
>-D WITH_OPENGL=ON \
>-D BUILD_TIFF=ON \
>-D WITH_FFMPEG=ON \
>-D WITH_GSTREAMER=ON \
>-D WITH_TBB=ON \
>-D BUILD_TBB=ON \
>-D BUILD_TESTS=OFF \
>-D WITH_V4L=ON \
>-D WITH_LIBV4L=ON \
>-D OPENCV_ENABLE_NONFREE=ON \
>-D INSTALL_C_EXAMPLES=OFF \
>-D INSTALL_PYTHON_EXAMPLES=OFF \
>-D BUILD_NEW_PYTHON_SUPPORT=ON \
>-D BUILD_opencv_python3=TRUE \
>-D OPENCV_GENERATE_PKGCONFIG=ON \
>-D BUILD_EXAMPLES=OFF \
> ..
>~~~
>
>~~~
>sudo make install -j8
>~~~
>
>~~~
>sudo apt install ros-melodic-filters
>sudo apt install ros-melodic-laser-geometry
>~~~

>## 0.2.2 Environment setup
>~~~
>/opt/ros/melodic/share/cv_bridge/cmake/cv_bridgeConfig.cmake
>sudo ln -s [OpenCV DIR] /usr/include/opencv
>sudo ldconfig
>sudo vim /usr/lib/pkgconfig/opencv.pc
>
>~~~

>## 0.2.3 Jetson Stats
>~~~
>sudo -H pip3 install jetson-stats
>jeson_release
>~~~

# 1. Install ROS (melodic)
http://wiki.ros.org/melodic/Installation/Ubuntu

>## 1.1 Setup your sources.list
>```
>sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
>```
>## 1.2 Set up your keys
>```
>sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
>```
>## 1.3 Installation
>```
>sudo apt update   
>sudo apt install ros-melodic-desktop-full
>```
>## 1.4 Environment setup
>```
>echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
>source ~/.bashrc
>source /opt/ros/melodic/setup.bash
>```
>## 1.5 Dependencies for building packages
>### python 2.7
>```
>sudo apt install python-rosdep python-rosinstall python-rosinstall-generator python-wstool build-essential
>sudo apt install python-rosdep
>sudo rosdep init
>rosdep update
>```
>### python 3.6
>```
>sudo apt-get install python3-pip python3-yaml
>sudo pip3 install rospkg catkin_pkg
>```
>## 1.6 Create a ROS Workspace
>```
>mkdir -p ~/catkin_ws/src
>cd ~/catkin_ws/
>catkin_make
>```
>### python3 option
>```
>catkin_make -DPYTHON_EXECUTABLE=/usr/bin/python3
>```
# 2. Install ROS Packages
>## 2.1 scale_truck_control
>```
>git clone https://github.com/HyeonGyu-Lee/scale_truck_control.git 
>```
>## 2.2 geometry_msgs
>```
>git clone https://github.com/ros/common_msgs.git
>```
>## 2.3 usb_cam
>```
>git clone https://github.com/ros-drivers/usb_cam.git -y
>```
>## 2.4 ros_rplidar
>```
>git clone https://github.com/robopeak/rplidar_ros.git
>```
>## 2.5 obstacle_detector
>```
>git clone https://github.com/tysik/obstacle_detector.git
>```
>## 2.6 laser_filters
>```
>git clone https://github.com/ros-perception/laser_filters.git 
>```
>## 2.7 vison_opencv (vision_opencv, image_geometry, cv_bridge)
>```
>git clone https://github.com/ros-perception/vision_opencv.git
>```

# 3. Install module & Environment setup
- melodic is ros-version (18.04 LTS)
>## 3.1 cv_bridge Setup
>```
>vim ~/catkin_ws/src/vision_opencv/cv_bridge/CMakelist.txt
>--find_package(Boost REQUIRED python37)
>++find_package(Boost REQUIRED python)
>vim ~/catkin_ws/src/vision_opencv/cv_bridge/src/module.hpp
>--static void * do_numpy_import( )
>++static void do_numpy_import( )
>--return nullptr;
>```
>## 3.2 OpenCV4 Setup
> ``` -y
>cd /opt/ros/melodic/share/cv_bridge/cmake
>sudo vim cv_bridgeConfig.cmake
>
>--set(_include_dirs "include;/usr/include;/usr/include/opencv
>++set(_include_dirs "include;/usr/include;/usr/include/opencv4
># Package Information for pkg-config
>```
>```
>sudo vim /usr/lib/pkgconfig/opencv.pc
>prefix=/usr/local
>exec_prefix=${prefix}libdir=${exec_prefix}/lib/aarch64-linux-gnu
>includedir_old=${prefix}/include/opencv4/opencv
>includedir_new=${prefix}/include/opencv4
>
>Name: OpenCV
>Description: Open Source Computer Vision Library
>Version: 4.4.0
>Libs: -L${exec_prefix}/lib/aarch64-linux-gnu -lopencv_dnn -lopencv_gapi -lopencv_highgui -lopencv_ml -lopencv_objdetect -lopencv_photo -lopencv_stitching -lopencv_video -lopencv_calib3d -lopencv_features2d -lopencv_flann -lopencv_videoio -lopencv_imgcodecs -lopencv_imgproc -lopencv_core
>Libs.private: -ldl -lm -lpthread -lrt
>Cflags: -I${includedir_old} -I${includedir_new}
> ```
> ```
>cd ~/catkin_ws/src/scale_truck_control
>vim CMakeLists.txt
>++set OpenCV_DIR /usr/share/opencv4
>--find_package(catkin REQUIRED COMPONENTS OpenCV REQUIRED }
>++find_package(OpenCV REQUIRED)
>```
>## 3.3 rosserial_Arduino Module
>- communicate with OpenCR 1.0
>```
>sudo apt-get install ros-melodic-rosserial-arduino   
>sudo apt-get install ros-melodic-rosserial   
>```
>## 3.4 armadillo , qtbase5 Module
>- Python UI Module -y
>```
>sudo apt-get install libarmadillo-dev
>sudo apt-get qtbase5-dev 
> ```
>## 3.5 alias command Setup
>```
>sudo vim ~/bashrc
>++alias cw='cd ~/catkin_ws'
>++alias cs='cd ~/catkin_ws/src'
>++alias cm='cd ~/catkin_ws && catkin_make'
>++alias cb='source ~/catkin_ws/devel/setup.bashrc'
>++alias sb='source ~/.bashrc'
>```

# 4. Run
>## 4.1 rosbag test
>- bag file download (2.2G)
>```
>curl -c ./cookie -s -L "https://drive.google.com/uc?export=download&id=1J7T7r9jhOk1YPxwyEDwj0IbOieedz049" > /dev/null
>curl -Lb ./cookie "https://drive.google.com/uc?export=download&confirm=`awk '/download/ {print $NF}' ./cookie`&id=1J7T7r9jhOk1YPxwyEDwj0IbOieedz049" -o "2020-10-06-17-23-14.bag"
>```
>## 4.2 Rosbag run
>```
>rosbag play 2020-10-06-17-23-14.bag
>```
>## 4.3 Ros Launch
>```
>sudo chmod 666 /dev/ACM0
>roslaunch scale_truck_control control_test.launch
>```
