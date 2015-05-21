## Install prerequisites

[gazebo5 is preferred](http://gazebosim.org/tutorials?tut=install_ubuntu&ver=5.0&cat=install).

## Setup catkin workspace

Create a catkin workspace for drcsim:
~~~
mkdir ~/catkin_drcsim
mkdir ~/catkin_drcsim/src
~~~

Next, [download drcsim_4.2.zip](https://app.box.com/files/0/f/0/1/f_30396417153).

~~~
cd ~/catkin_drcsim/src
unzip [path to download location]/drcsim_4.2.zip
~~~

or alternatively checkout code from bitbucket (Warning: may take a long time):

~~~
cd ~/catkin_drcsim/src
hg clone ssh://hg@bitbucket.org/osrf/drcsim -b drcsim_4.2
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
~~~

