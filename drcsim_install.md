## Install prerequisites

[gazebo5 is preferred](http://gazebosim.org/tutorials?tut=install_ubuntu&ver=5.0&cat=install).


Install osrf-messages and sandia-hands

~~~
sudo apt-get install osrf-common sandia-hand
~~~

## Setup catkin workspace

Create a catkin workspace for drcsim:
~~~
mkdir ~/catkin_drcsim
mkdir ~/catkin_drcsim/src
~~~

Next, [follow this link to download drcsim_4.2.zip tarball](https://app.box.com/files/0/f/0/1/f_30396417153).

~~~
cd ~/catkin_drcsim/src
unzip [path to download location]/drcsim_4.2.zip
~~~

or alternatively checkout code from bitbucket (Warning: may take a long time):

~~~
cd ~/catkin_drcsim/src
hg clone ssh://hg@bitbucket.org/osrf/drcsim -b drcsim_4.2
~~~

Checkout gazebo_ros_pkgs from source:
~~~
git clone -b indigo-devel git@github.com:ros-simulation/gazebo_ros_pkgs
~~~

## Build drcsim code

To compile the drcsim package:
~~~
cd ~/catkin_drcsim
source /opt/ros/indigo/setup.bash
catkin_make install
~~~

## Setup environment variables

To run the drcsim example, setup environment variable by running:
~~~
source ~/catkin_drcsim/install/setup.bash
source ~/catkin_drcsim/install/share/drcsim/setup.sh
~~~

Test with:

~~~
roslaunch drcsim_gazebo atlas.launch hand_suffix:="_sandia_hands"
~~~

The robot should spawn and fall over without the Boston Dynamics controller enabled.
