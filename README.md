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
Changing the directory name(to avoid the conflict with the leo_description pkg in /share,which I suppose was implemented with the leo_viz pkg) and modifying the absolute address into $(find) substitute relating to .stl and .dae models, I finished it. The crucial part is to change the names of topic into '/model/leo_rover/...' format in gazebo sim pluggins and remap them in launch file(and I don't know why). Check the topic and tf, healthy.
There is seemingly no difference bewtween
```
<plugin filename="ignition-gazebo-diff-drive-system" 
    name="ignition::gazebo::systems::DiffDrive">
```
and
```
<plugin filename="libignition-gazebo-diff-drive-system.so"
    name="ignition::gazebo::systems::DiffDrive">
```
Watching the screenshots of the urdf file in the raspberry pi of the leo rover from the last lab session, I noticed that there are still many plugins for gazebo not been implemented in my .xacro file.
Next step is to mount a lidar on the planar and activate it in gazebo.
### 18:04
When I tried to fix the problem that gazebo can't publish imu and lidar data which is definitely the issues relating to the plugins, I found myself failed to load the plugin lib as I copy codes from the urdf file in the raspberry pi.
'''xml
  <!-- Imu -->
    <gazebo reference="${link_prefix}imu_frame">
      <sensor type="imu" name="leo_imu_sensor">
        <update_rate>100</update_rate>
        <plugin filename="libgazebo_ros_imu_sensor.so"
          name="imu_plugin">
          <topicName>${topic_prefix}imu/data_raw</topicName>
          <updateRateHZ>100.0</updateRateHZ>
          <initialOrientationAsReference>false</initialOrientationAsReference>
        </plugin>
      </sensor>
    </gazebo>
```
```bash
[Err] [SystemLoader.cc:94] Failed to load system plugin [libgazebo_ros_imu_sensor.so] : couldn't find shared library.
```
leo_viz
