# leo_log

## 2ed, March
### 12:57
Given the incompatibility btween two different versions of Gazebo, I dicide to use the gazebo lib diff-control plugins for simulation.
VERSION={'Ubuntu':(22.04, Jammy), 'ros':humble, 'Gazebo':(11, fortress), 'gz':ignition-gazeb06}
### 13:23 
Afer removing the self-defined differential control plugin, I successfully launch the model in Gazebo simulaiton, however, encountering the error:
Failed to load system plugin [libleo_gz_differential_plugin.so] : couldn't find shared library.
ROS_IP:127.0.0.1
source /opt/ros/humble/setup.bash
ros2 launch leo_gz_bringup leo_gz.launch.py
### 13:53 
launch the diff demo of gazebo and test it. The conclusion is that when you get error in the lost of the package that you just apt install, shutting down and rebooting will be fine.
ros2 topic info /clock -v
### 15:52 
Compare the differences between the gz_example_description_package and my leo simualtion package. Try to realize the diff control without leo diff control plugins.
### 16:40 
And I finished it. Check the topic and tf.
There is seemingly no difference bewtween
<plugin filename="ignition-gazebo-diff-drive-system" 
    name="ignition::gazebo::systems::DiffDrive"> and
<plugin filename="libignition-gazebo-diff-drive-system.so"
    name="ignition::gazebo::systems::DiffDrive">
Next step is to mount a lidar on it.
