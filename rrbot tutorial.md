Install the prerequisites:
~~~
sudo apt-get install ros-indigo-gazebo4-ros-pkgs ros-indigo-gazebo4-ros-control
~~~

First create a catkin workspace for rrbot example:
~~~
mkdir ~/catkin_rrbot
mkdir ~/catkin_rrbot/src
~~~

Next, checkout the rrbot demo files:
~~~
cd ~/catkin_rrbot/src
git clone -b cpp11_flags git@github.com:ros-simulation/gazebo_ros_demos.git
~~~

To compile the rrbot example:
~~~
cd ~/catkin_rrbot
source /opt/ros/indigo/setup.bash
catkin_make
~~~

