FROM osrf/ros:humble-desktop

SHELL ["/bin/bash", "-c"]

RUN  apt-get update \
    && apt-get install -y \
        pulseaudio

# clone source
ENV ROS_WS /home/pose_detection

RUN mkdir -p $ROS_WS/src
WORKDIR $ROS_WS/src
RUN git clone https://github.com/olaghattas/human_pose_detection.git
RUN git clone https://github.com/olaghattas/opencv_cam.git
RUN git clone https://github.com/ptrmu/ros2_shared.git
RUN git clone https://github.com/AprilRobotics/apriltag.git
RUN git clone https://github.com/olaghattas/apriltag_ros.git

WORKDIR $ROS_WS

# for rviz visualization
RUN apt update
RUN apt install python3-pip -y
RUN apt-get install software-properties-common -y
RUN add-apt-repository ppa:kisak/kisak-mesa
RUN apt update
RUN apt upgrade -y
RUN apt install nano
RUN apt-get install ros-humble-rqt

RUN apt-get install python3-dev libffi-dev libssl-dev libudev-dev

# get dependencies
RUN apt-get install libportaudio2
RUN pip3 install playsound
RUN pip3 install mediapipe
RUN apt update
RUN rosdep update
RUN rosdep install --from-paths src --ignore-src -y

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

ENTRYPOINT ["/ros_entrypoint.sh"]

CMD ["bash"]
