FROM ros:foxy as dev

ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /root/ros2_ws/

RUN apt update && apt install -y language-pack-ja
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 42D5A192B819C5DA \
    && apt-get update \
    && apt-get install -qq -y --no-install-recommends \
    build-essential \
    cmake \
    git \
    libssl-dev \
    libusb-1.0-0-dev \
    pkg-config \
    libgtk-3-dev \
    libglfw3-dev \
    libgl1-mesa-dev \
    libglu1-mesa-dev \    
    curl \	
    libusb-1.0-0 \
    udev \
    apt-transport-https \
    ca-certificates \
    curl \
    vim \
    swig \
    software-properties-common \
    python3-pip \
    ros-foxy-joint-state-publisher-gui \
    ros-foxy-rqt-robot-steering \
    ros-foxy-teleop-twist-keyboard\
    ros-foxy-cartographer-ros \
    ros-foxy-navigation2 \
    ros-foxy-nav2-bringup \
    ros-foxy-rviz2 \
    ros-foxy-v4l2-camera \
    ros-foxy-compressed-image-transport \
    ros-foxy-image-transport-plugins \
    python3-pyaudio \
    sox \ 
    libsox-fmt-mp3 \
    alsa-base \
    alsa-utils \
    && rm -rf /var/lib/apt/lists/*

RUN python3 -m pip install gtts
RUN python3 -m pip install gtts-token

RUN git clone https://github.com/YDLIDAR/YDLidar-SDK.git &&\
    mkdir -p YDLidar-SDK/build && \
    cd YDLidar-SDK/build &&\
    cmake ..&&\
    make &&\
    make install &&\
    cd .. &&\
    pip install . &&\
    cd .. && rm -r YDLidar-SDK 

RUN mkdir -p /root/ros2_ws/src/ && \
    . /opt/ros/foxy/setup.sh && \
    cd /root/ros2_ws/src && \
    git clone -b foxy https://github.com/agilexrobotics/limo_ros2.git && \
    cd /root/ros2_ws && \
    colcon build && \
    bash /root/ros2_ws/install/setup.bash

RUN echo "source /opt/ros/foxy/setup.bash" >> /etc/bash.bashrc && \
    echo "source /root/ros2_ws/install/setup.bash" >> /etc/bash.bashrc



# ** [可选] 取消注释安装其他组件 **
#
# ENV DEBIAN_FRONTEND=noninteractive
# RUN apt-get update \
#    && apt-get -y install --no-install-recommends <your-package-list-here> \
#    #
#    # Clean up
#    && apt-get autoremove -y \
#    && apt-get clean -y \
#    && rm -rf /var/lib/apt/lists/*
# ENV DEBIAN_FRONTEND=dialog

# 配置自动 source

