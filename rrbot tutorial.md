## Install the prerequisites:
~~~
sudo apt-get install ros-indigo-gazebo4-ros-pkgs ros-indigo-gazebo4-ros-control
~~~

## Setup workspace

Create a catkin workspace for rrbot example:
~~~
mkdir ~/catkin_rrbot
mkdir ~/catkin_rrbot/src
~~~

Next, checkout the rrbot demo files:
~~~
cd ~/catkin_rrbot/src
git clone -b cpp11_flags git@github.com:ros-simulation/gazebo_ros_demos.git
~~~

## Build example code

To compile the rrbot example:
~~~
cd ~/catkin_rrbot
source /opt/ros/indigo/setup.bash
catkin_make
~~~

## Setup environment variables

To run the rrbot example, setup environment variable by running:
~~~
source ~/catkin_rrbot/devel/setup.bash
~~~

## Start the controllers using roslaunch

Test the RRBot controlled by ros_control by running the following:

Start the RRBot simulation:

~~~
roslaunch rrbot_gazebo rrbot_world.launch
~~~

Load the controllers for the two joints by running the second launch file:

~~~
roslaunch rrbot_control rrbot_control.launch 
~~~

### Using service calls manually

If you first load the rrbot_control.yaml files to the parameter server, you could load the controllers manually through service requests.
We'll include them here for reference though we usually prefer roslaunch:

Load the controllers:

~~~
rosservice call /rrbot/controller_manager/load_controller "name: 'joint1_position_controller'"
rosservice call /rrbot/controller_manager/load_controller "name: 'joint2_position_controller'"
~~~

Start the controllers:

~~~
rosservice call /rrbot/controller_manager/switch_controller "{start_controllers: ['joint1_position_controller','joint2_position_controller'], stop_controllers: [], strictness: 2}"
~~~

Stop the controllers:

~~~
rosservice call /rrbot/controller_manager/switch_controller "{start_controllers: [], stop_controllers: ['joint1_position_controller','joint2_position_controller'], strictness: 2}"
~~~

## Manually send example commands

Send example joint commands to them for testing:

~~~
rostopic pub -1 /rrbot/joint1_position_controller/command std_msgs/Float64 "data: 1.5"
rostopic pub -1 /rrbot/joint2_position_controller/command std_msgs/Float64 "data: 1.0"
~~~

## Use RQT To Send Commands

In this section we'll go over tools to help you visualize the performance of your controller and tune any gains/parameters the controller might have, particularly PID gains.
We'll be using [RQT](http://www.ros.org/wiki/rqt), ROS's plugin-based user interface, so be sure you first have that installed.

Start RQT:

~~~
rosrun rqt_gui rqt_gui
~~~

### Add a Command Publisher

On the 'Plugins' menu of RQT add the 'Topics->Message Publisher' plugin then choose the topic from the drop down box that commands any particular controller that you want to publish to.
For the RRBot, add the controller:

~~~
/rrbot/joint1_position_controller/command
~~~

Then press the green plus sign button at the top right.

Enable the topic publisher by checking the check box on the left of the topic name.
Set the rate column to 100 (the frequency we send it commands - 100hz in this case). 

Next, expand the topic so that you see the "data" row.
In the expression column, on the data row, try different radian values between joint1's joint limits - in RRBot's case there are no limits because the joints are continuous, so any value works.
You should be able to get the RRBot to swing around if you are doing this tutorial with that robot.

Next, in that same expression box we'll have it automatically change values using a sine wave.
Add the following:

~~~
sin(i/100)
~~~

For more advanced control, you can configure it to publish a sine wave to your robot's exact joint limits:

~~~
sin(i/rate*speed)*diff + offset
~~~

An explanation of variables:

 * i - the RQT variable for time
 * rate - the frequency that this expression is evaluated. This should be the same number as in the rate column of the topic publisher. Recommended value is 100.
 * speed - how quick you want the join to actuate. Start off with just 1 for a slow speed
 * upper\_limit and lower\_limits - the joint limits of the hardware being controlled by this controller
 * diff = (upper\_limit - lower\_limit)/2 
 * offset = upper_limit-diff

### Visualize the controller's performance

Add a Plot plugin to RQT and add the same topic as the one you chose above for the topic publisher:

~~~
/rrbot/joint1_position_controller/command/data
~~~

Click the green add button.
You should now see a sine wave being plotted on the screen.

Add another topic to the Plot plugin that tracks the error between the commanded position and actual position of the actuator being controlled.
For the RRBot:

~~~
/rrbot/joint1_position_controller/state/error
~~~

You screen should look something like this:

[[file:rqt_controller_tuning.png|800px]]

Note: the RQT plot plugin is known to have bugs after running for a while (>1min).
The drawings start acting strangely.
The current solution is to press the blue refresh button at the top right of the plugin.

### Tune the PID gains

Finally, we'll use [dynamic reconfigure](http://ros.org/wiki/dynamic_reconfigure) to tune the proportional, derivative, and integral gains of the PID controller, assuming this is applicable to your robot.

Add the 'Dynamic Reconfigure' plugin to RQT and click 'Expand All' to see the sub-options.
Assuming your controller uses PID, you should use a "pid" option.
Clicking on it should reveal 5 sliders that let you tune the controller, as pictured in the following screenshot.
Adjust these values until you get the desired performance of your controller.

[[file:rqt_dynamnic_reconfigure_pid.png|600px]]

### Use roslaunch to save your RQT perspective

A pre-configured RQT perspective for the rrbot can be easily launched with the following command:

~~~
roslaunch rrbot_control rrbot_rqt.launch
~~~

You can use that as a template for doing this with your own robot.

## Connect Rviz to Gazebo Simulation

Now that you are using ros\_control to send commands to your robot in simulation, you can also use the ros\_control joint_state_controller to read the state of the robot from Gazebo.
The idea behind a good simulator is that you should be able to use the same software on your real hardware as you do in simulation.
A good starting point for that is visualizing your simulated robot in Rviz, similar to how it is done with real hardware.

Assuming you are already starting a joint\_state\_controller as documented above in your rosparam and roslaunch files, your next step is to start Rviz:

~~~
rosrun rviz rviz
~~~

Under "Global Options" change your "Fixed Frame" to "world" to resolve any errors it might be giving you.

Next, add a "RobotModel" display type to Rviz and you should then see your simulated robot in Gazebo being visualized in Rviz!

