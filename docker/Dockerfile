FROM ros:humble

ARG ROS2_DISTRO=humble

RUN apt-get update && apt-get install -y \
    ros-humble-ros-base \
    python3-colcon-common-extensions \
    python3-pip \
    build-essential \
    git \
    ros-humble-rmw-cyclonedds-cpp \
    ros-humble-navigation2 \
    ros-humble-nav2-bringup \
    ros-humble-slam-toolbox \
    ros-humble-tf2-ros \
    ros-humble-tf2-geometry-msgs \
    ros-humble-tf-transformations \
    ros-humble-robot-localization \
    ros-humble-teleop-twist-* \
    ros-humble-mavros \
    ros-humble-plotjuggler-ros \
    ros-humble-rosbag2-storage-mcap \
    ros-humble-rqt* \
    ros-humble-nmea-msgs \
    ros-humble-mavros-msgs \
    apt-transport-https \
    cmake \
    libasio-dev \
    libtinyxml2-dev \
    libboost-all-dev \
    nano \
    vim \
    can-utils \
    usbutils \
    iproute2 \
    net-tools \
    tmux \
    kmod \
    tmuxinator \
    && rm -rf /var/lib/apt/lists/*


# Setup timezone (fix interactive package installation)
RUN ln -snf /usr/share/zoneinfo/${TZ} /etc/localtime && echo ${TZ} > /etc/timezone
#RUN ip link set can0 up type can bitrate 500000

# Agent forwarding during docker build https://stackoverflow.com/questions/43418188/ssh-agent-forwarding-during-docker-build
# install ssh client and git
RUN sudo apt-get install openssh-client git

ENV DOCKER_BUILDKIT=1
RUN apt-get install -y openssh-client
ENV GIT_SSH_COMMAND="ssh -v" 
USER root

RUN --mount=type=ssh id=default \
    mkdir -p -m 0600 ~/.ssh/ && \
    ssh-keyscan github.com >> ~/.ssh/known_hosts


RUN apt-get update


# Livox SDK
WORKDIR /root
RUN git clone https://github.com/mkozlik/ranger_startup.git
RUN git clone https://github.com/Livox-SDK/Livox-SDK2.git
WORKDIR /root/Livox-SDK2
RUN mkdir build
WORKDIR /root/Livox-SDK2/build
RUN cmake .. && make -j
RUN sudo make install


# Set up a workspace
WORKDIR /root/ranger_ws/src
RUN git clone https://github.com/westonrobot/ugv_sdk.git
RUN git clone https://github.com/agilexrobotics/ranger_ros2.git
RUN git clone https://github.com/Kinovarobotics/ros2_kortex.git
RUN git clone --branch humble https://github.com/ros-perception/pointcloud_to_laserscan.git

# Go to workspace and build it
WORKDIR /root/ranger_ws/
RUN /bin/bash -c "source /opt/ros/humble/setup.bash; colcon build --symlink-install"

#RUN bash /root/ranger_ws/src/ranger_ros2/ranger_bringup/scripts/setup_can2usb.bash

#WORKDIR /root/ranger_startup

CMD ["bash"]
