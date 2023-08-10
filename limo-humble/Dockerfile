#FROM dustynv/ros:humble-desktop-l4t-r32.7.1 as dev
FROM ros:humble as dev

ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /root/ros2_ws/

#RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key  -o /usr/share/keyrings/ros-archive-keyring.gpg \
#   && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/ros2.list > /dev/null

RUN apt-get update && \
    apt-get install -qq -y --no-install-recommends \
    build-essential \
    cmake \
    git \
    curl \
    libssl-dev \
    libusb-1.0-0-dev \
    pkg-config \
    libgtk-3-dev \
    libglfw3-dev \
    libgl1-mesa-dev \
    libglu1-mesa-dev \
    libusb-1.0-0 \
    udev \
    apt-transport-https \
    ca-certificates \
    curl \
    swig \
    software-properties-common \
    python3-pip \
    ros-humble-joint-state-publisher-gui \
    ros-humble-rqt-robot-steering \
    ros-humble-teleop-twist-keyboard\
    ros-humble-cartographer-ros \
    ros-humble-navigation2 \
    ros-humble-nav2-bringup \
    && rm -rf /var/lib/apt/lists/*

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
    . /opt/ros/humble/setup.sh && \
    cd /root/ros2_ws/src && \
    git clone -b humble-dev https://github.com/agilexrobotics/limo_ros2.git && \
    cd /root/ros2_ws && \
    colcon build && \
    bash /root/ros2_ws/install/setup.bash

RUN echo "source /opt/ros/humble/setup.bash" >> /etc/bash.bashrc && \
    echo "source /root/ros2_ws/install/setup.bash" >> /etc/bash.bashrc

