FROM osrf/ros2_packaging_aarch64_jammy_39_snapshot

SHELL ["/bin/bash", "-c"]

RUN  apt-get update \
    && apt-get install -y
     

# clone source
ENV ROS_WS /home/pose_detection

RUN mkdir -p $ROS_WS/src
WORKDIR $ROS_WS/src
RUN git clone https://github.com/olaghattas/human_pose_detection.git
RUN git clone https://github.com/olaghattas/opencv_cam.git
RUN git clone https://github.com/ptrmu/ros2_shared.git
RUN git clone https://github.com/AprilRobotics/apriltag.git
RUN git clone https://github.com/olaghattas/apriltag_ros.git

# install humble 
RUN apt update && sudo apt install locales -y
RUN locale-gen en_US en_US.UTF-8
RUN update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
RUN export LANG=en_US.UTF-8

RUN apt install software-properties-common -y
RUN add-apt-repository universe

RUN apt update && sudo apt install curl -y
RUN sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(. /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
RUN apt update
RUN apt upgrade
RUN apt install ros-humble-ros-base -y

WORKDIR $ROS_WS

# for rviz visualization
RUN apt update
RUN apt install python3-pip -y
RUN apt-get install software-properties-common -y
RUN add-apt-repository ppa:kisak/kisak-mesa
RUN apt update
RUN apt upgrade -y
RUN apt install nano
RUN apt-get install ros-humble-rqt -y
RUN apt-get install pulseaudio -y

RUN apt-get install python3-dev libffi-dev libssl-dev libudev-dev -y

# get dependencies
RUN apt-get install libportaudio2 -y
RUN pip3 install playsound
RUN pip3 install mediapipe
RUN apt update
RUN apt-get install python3-rosdep -y
RUN pip install rosdep
RUN rosdep init
RUN rosdep update
# Set ROS_PYTHON_VERSION to 3
ENV ROS_PYTHON_VERSION=3
# Set ROS_DISTRO to your desired ROS distribution
ENV ROS_DISTRO=humble
RUN apt install python3-colcon-common-extensions -y
RUN rosdep install --from-paths src --ignore-src -y

RUN apt-get install ros-humble-rclcpp-components -y

RUN cd ${ROS_WS} \
    && . /opt/ros/humble/setup.sh \
    && colcon build \
    && source ${ROS_WS}/install/setup.bash

RUN apt-get install vlc -y

# copy entry pointer script
RUN apt install net-tools -y
RUN apt install tmux -y
RUN apt-get install iputils-ping -y
RUN adduser root dialout
RUN sudo sed -i 's/geteuid/getppid/' /usr/bin/vlc
COPY ros_entrypoint.sh /
RUN rm -rf /var/lib/apt/lists/*
RUN echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc

ENTRYPOINT ["/ros_entrypoint.sh"]

CMD ["bash"]
