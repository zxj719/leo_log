# leo_log

## 2ed, March
### 12:57
Given the incompatibility btween two different versions of Gazebo, I dicide to use the gazebo lib diff-control plugins for simulation.
VERSION={'Ubuntu':(22.04, Jammy), 'ros':humble, 'Gazebo':(11, fortress), 'gz':ignition-gazebo6}
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
```xml
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
## 3rd, March
### 11:04
Neither imu nor lidar sensor could be simulated in gazebo yesterday, and there was no relevant published topic as consequences. Today, imu part goes well abruptly.
WTF The codes order matters! I have to put the plugin block within the sensor block like this:
```xml
 <gazebo reference="${link_prefix}imu_frame">
      <sensor type="imu" name="leo_imu_sensor">
      <plugin filename="libignition-gazebo-imu-system.so"
        name="ignition::gazebo::systems::Imu">
      </plugin>
        <update_rate>100</update_rate>
        <visualize>true</visualize>
        <topic>${topic_prefix}imu/data_raw</topic>
        <always_on>1</always_on>
        <ignition_frame_id>${link_prefix}imu_frame</ignition_frame_id>
      </sensor>
    </gazebo>
```
Or I would fail to launch this part in gazebo.
### 11:43 
Now the lidar and imu can be safely implemented and launched. New slight issue is there existing a minor upper angle, that is a pitch angle of the body so that the lidar also represents a inclined scan process.
And I fixed it by changing the rocker joint parameters from 
```xml
<limit effort="100.0" lower="-0.24" upper="0.24" velocity="100.0"/>
```
to
```xml
<limit effort="100.0" lower="-0" upper="0" velocity="100.0"/>
```
### 12:02
And after 3 weeks, I can finally bond the /map from slam_toolbox! God bless group 4! Next step would be much easier I suppose, since I've been tortured for over 16 hours on this issue and tried at least 5 approaches.
### 22:33 
Separate the lidar urdf part from the original macro.xacro file for later implementation in real world. And seperate the slam_toolbox from that launch file for the same reason and add the map_saver_server.Adding keyboard and joystick control to the slam and map_maker launch.py.

## 4th, March
### 18:44
Try to fix the visualization problem in rviz2. If you want vscode to view .rviz file as yaml format, add it to the setting.json
```json
"files.associations": {
        "*.rviz": "yaml",
        "*.sdf": "xml",
        
    },
```
### 19:10 
It turns out changing \\$(find my_leo) into file://\\$(find my_leo) in urdf can fix the problem that rviz2 can't locate the .stl and .dae files.
### 20:13 
Implemented bt, planner and controller into map_maker.launch.py, successfully achieved navigation by A*. Next, learn more about nav2 and frontier algorithm, gmapping.

## 5th, March
### 18:24
Joystick, first connect with the ros2 system. Maybe wait for tomerrow when I get the JOY.
### 19:15 
Added a red cube object in Gazebo. 
### 22:33 
Prepared for tomorrow lab session. Packed up several pkgs in order to make the leo slam successfully. Merge nav2.rviz into robot.rviz.
1. Tomorrow, we will first try to use the laser.launch.xml in my teleop_leo repo without the sourcing rplidar_create_rules.sh, or we need to put the source file into /etc self start file along with the robot starting itself in raspberri pi.
2. Secondly, set up laser.launch.xml and laser.urdf.xacro and launch then in robot urdf file and robot.launch.xml respectively.
3. Thirdly, we need to connect joy or keyboard with nuc and send order. 
4. Lastly, I need to convert real_leo launch files from .launch to .xml.

## 6th, March
### 19:08 
Today lab session, I still got stuck in the problem that /tf topic won't publish the transformation between lidar and base_link, and I did what I had planned, but it didn't work. The gap bwtween the simulation and reality is wider than i expected. Now, I would focus more on the simulaiton and find a way to realize the task that the robot should perform an automous navigation to find the object.

leo_viz

