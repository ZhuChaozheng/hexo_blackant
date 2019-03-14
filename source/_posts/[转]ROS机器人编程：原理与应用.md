
---
title: 『转』ROS机器人编程：原理与应用
date: 2019-03-12 19:34:58
tags: ROS
mathjax: true
---

##A Systematic Approach to Learning Robot Programming with ROS
(原网址：<https://zhangrelay.blog.csdn.net/article/details/88359066?from=singlemessage>)
<!--more-->
***

![](/img/CSDN1.png)
![](/img/CSDN2.png)
***

本书对于代码的讲解详细具体，个人大爱，以下是译者序：

![](/img/CSDN3.jpg)

***

本书完全适用于使用indigo或kinetic的学习者或开发者，部分适用Melodic。
##Ubuntu 14.04 和 ROS Indigo：
   1.学习源码：<https://github.com/wsnewman/learning_ros>
   2.扩展资源：<https://github.com/wsnewman/learning_ros_external_packages>
##Ubuntu 16.04 和 ROS Kinetic：
   1.学习源码：<https://github.com/wsnewman/learning_ros_kinetic>
   2.扩展资源：<https://github.com/wsnewman/learning_ros_external_pkgs_kinetic>
***
##本书设置脚本

这里的脚本假设您在github上有一个帐户（在github.com上是免费的）。

您需要在计算机上安装“git”才能使用这些脚本。如果您的计算机上尚未安装“git”，则可以安装：

`sudo apt-get --yes --force-yes install git`
***
##获取脚本并使其可执行

要运行这些脚本，请在任何目录中键入以下内容，在计算机上的任何位置克隆learning_ros_setup_scripts：

`git clone https://github.com/wsnewman/learning_ros_setup_scripts.git`

获得文件后，learning_ros_setup_scripts键入以下命令更改目录： 

`cd learning_ros_setup_scripts`

在此之后，键入以下命令使脚本可执行： 

`chmod +x *.sh`

##ROS设置

要使用“学习ROS”设置所有依赖项和程序的Indigo的ROS安装，请install_ros_and_tools.sh键入以下命令运行脚本:

`(./install_ros_and_tools.sh 或bash install_ros_and_tools.sh）`

对于ROS Kinetic使用此行:(与Ubuntu 16.04一起使用） 

`./install_ros_and_tools_kinetic.sh`

##工作站设置

要为ROS Indigo设置ROS工作区，请使用该setup_workspace_learning_ros.sh脚本。您需要将您的github用户名和电子邮件作为参数传递给脚本:

  `( ./setup_workspace_learning_ros.sh github_username github@email.com `

或

`bash setup_workspace_learning_ros.sh github_username github@email.com）`

其中github_username是您在github上的用户名，github @ email.com是您与github帐户关联的电子邮件地址。

对于ROS Kinetic，请使用以下行：

`./setup_workspace_learning_ros_kinetic.sh github_username github@email.com`

***
Kinetic

```
#!/bin/bash
# Learning ROS
# ROS + Dependencies Installation
# v 0.36
 
echo "Beginning ROS Installation"
 
echo -e "\e[1m \e[34m >>> Beginning ROS Kinetic Installation \e[21m \e[39m"
echo -e "\e[34m >>> Setting up sources.list and keys... \e[39m"
 
  sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
  sudo apt-key adv --keyserver hkp://pool.sks-keyservers.net --recv-key 0xB01FA116
 
echo -e "\e[34m >>> ...done\e[39m"
 
  sudo apt-get update
 
echo -e "\e[34m >>> Beginning ros-kinetic-desktop-full installation...\e[39m"
 
  sudo apt-get --yes --allow install ros-kinetic-desktop-full 
 
echo -e "\e[34m >>> Setting up rosdep\e[39m"
 
  sudo rosdep init
  rosdep update
 
echo -e "\e[34m >>> Setting up environment \e[39m"
 
  echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
  source ~/.bashrc
 
echo -e "\e[34m >>> Setting up rosinstall \e[39m"
 
  sudo apt-get --yes --force-yes install python-rosinstall
 
echo -e "\e[1m \e[34m >>> Installing dependencies for mobile robotics code \e[21m \e[39m"
 
sudo apt-get --yes --force-yes install ros-kinetic-joy ros-kinetic-costmap-2d ros-kinetic-nav-core ros-kinetic-sound-play ros-kinetic-amcl ros-kinetic-slam-gmapping ros-kinetic-move-base ros-kinetic-controller-interface ros-kinetic-gazebo-ros-control ros-kinetic-joint-state-controller ros-kinetic-effort-controllers ros-kinetic-moveit-msgs ros-kinetic-stdr-simulator ros-kinetic-teleop-twist-keyboard ros-kinetic-slam-gmapping ros-kinetic-map-server ros-kinetic-qt-build ros-kinetic-kdl-parser ros-kinetic-combined-robot-hw ros-kinetic-combined-robot-hw-tests ros-kinetic-controller-manager-tests ros-kinetic-diff-drive-controller ros-kinetic-force-torque-sensor-controller ros-kinetic-gripper-action-controller ros-kinetic-imu-sensor-controller ros-kinetic-position-controllers ros-kinetic-ros-control ros-kinetic-ros-controllers ros-kinetic-rqt-joint-trajectory-controller ros-kinetic-velocity-controllers
sudo apt-get --yes --force-yes install ros-kinetic-cv-bridge ros-kinetic-polled-camera ros-kinetic-camera-info-manager ros-kinetic-tf-conversions
sudo apt-get --yes --force-yes install ros-kinetic-opencv3 libopencv-dev
 
sudo apt-get --yes --force-yes install ros-kinetic-ur-description ros-kinetic-ur-gazebo
  
echo -e "\e[1m \e[34m >>> Installing support software \e[21m \e[39m"
 
  sudo apt-get --yes --force-yes install git
  sudo add-apt-repository -y ppa:webupd8team/sublime-text-3
  sudo apt-get update
  sudo apt-get --yes --force-yes install sublime-text-installer 
 
  sudo apt-get --yes --echo -e "\e[34m >>> Setting up rosinstall \e[39m"
 
  sudo apt-get --yes --force-yes install python-rosinstall install netbeans
  sudo apt-get --yes --force-yes install gitk git-gui
  sudo apt-get --yes --force-yes install kazam vlc
  
  
echo -e "\e[1m \e[34m >>> Installing rqt \e[21m \e[39m"
  sudo apt-get --yes install ros-kinetic-rqt
  sudo apt-get --yes install ros-kinetic-rqt-common-plugins
 
  source ~/.bashrc
 
source ~/ros_ws/devel/setup.bash

```

***
更多详细资料和教程，稍后补充
