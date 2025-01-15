# launch文件使用

## 1 启动单机sitl实例launch文件

参考`PX4-Autopilot/launch`目录下`mavros_posix_sitl_launch.launch`文件：
```
<?xml version="1.0"?>
<launch>
    <!-- MAVROS posix SITL environment launch script -->
    <!-- launches MAVROS, PX4 SITL, Gazebo environment, and spawns vehicle -->
    <!-- vehicle pose -->
    <arg name="x" default="0"/>
    <arg name="y" default="0"/>
    <arg name="z" default="0"/>
    <arg name="R" default="0"/>
    <arg name="P" default="0"/>
    <arg name="Y" default="0"/>
    <!-- vehicle model and world -->
    <arg name="est" default="ekf2"/>
    <arg name="vehicle" default="iris"/>
    <arg name="world" default="$(find mavlink_sitl_gazebo)/worlds/empty.world"/>
    <arg name="sdf" default="$(find mavlink_sitl_gazebo)/models/$(arg vehicle)/$(arg vehicle).sdf"/>

    <!-- gazebo configs -->
    <arg name="gui" default="true"/>
    <arg name="debug" default="false"/>
    <arg name="verbose" default="false"/>
    <arg name="paused" default="false"/>
    <arg name="respawn_gazebo" default="false"/>
    <!-- MAVROS configs -->
    <arg name="fcu_url" default="udp://:14540@localhost:14557"/>
    <arg name="respawn_mavros" default="false"/>
    <!-- PX4 configs -->
    <arg name="interactive" default="true"/>
    <!-- PX4 SITL and Gazebo -->
    <include file="$(find px4)/launch/posix_sitl.launch">
        <arg name="x" value="$(arg x)"/>
        <arg name="y" value="$(arg y)"/>
        <arg name="z" value="$(arg z)"/>
        <arg name="R" value="$(arg R)"/>
        <arg name="P" value="$(arg P)"/>
        <arg name="Y" value="$(arg Y)"/>
        <arg name="world" value="$(arg world)"/>
        <arg name="vehicle" value="$(arg vehicle)"/>
        <arg name="sdf" value="$(arg sdf)"/>
        <arg name="gui" value="$(arg gui)"/>
        <arg name="interactive" value="$(arg interactive)"/>
        <arg name="debug" value="$(arg debug)"/>
        <arg name="verbose" value="$(arg verbose)"/>
        <arg name="paused" value="$(arg paused)"/>
        <arg name="respawn_gazebo" value="$(arg respawn_gazebo)"/>
    </include>
    <!-- MAVROS -->
    <include file="$(find mavros)/launch/px4.launch">
        <!-- GCS link is provided by SITL -->
        <arg name="gcs_url" value=""/>
        <arg name="fcu_url" value="$(arg fcu_url)"/>
        <arg name="respawn_mavros" value="$(arg respawn_mavros)"/>
    </include>
</launch>

```

更改不同飞机类型可以将
```
<arg name="vehicle" default="iris"/>
```
改为固定翼：
```
<arg name="vehicle" default="plane"/>
```
改为垂起：
```
<arg name="vehicle" default="standard_vtol"/>
```
其他更多机型可以参考PX4-AUTOPILOT目录内的gazebo目录下其他机型，以及查看PX4官方user guide提供的模型。


## 2 启动多机sitl实例launch文件

参考`PX4-Autopilot/launch`目录下`multi_uav_sitl_launch.launch`文件，原始文件为3机sitl，若需要添加额外飞机，参考文件末尾注释：
```
<!-- to add more UAVs (up to 10):
Increase the id
Change the name space
Set the FCU to default="udp://:14540+id@localhost:14550+id"
Set the malink_udp_port to 14560+id -->
```
这里注意往后添加FCU的udp端口，从第四架次后面为`"udp://:14540+id@localhost:14550+id"`，详细可参考提供的`px4_launch/6_vtol.launch`。最多仅支持10架多机，超过10架次会导致端口冲突，需要重新修改PX4自身端口配置。

## 3 单机控制launch文件

1.参考`single_demoo/launch/plane_circle_track.launch`文件：
```
<launch>
	<group>
		<node name="single_plane" pkg="uav_controller" type="vector_fw.py" output="screen"/>
		<node name="cmd_node" pkg="single_demo" type="circle_track.py" output="screen"/>
	</group>
</launch>
```
2.分别启动两个功能包的节点文件：  
- `vector_fw.py`改为`vector_vtol.py`即可切换为垂起无人机；  
- `circle_track.py`改为`single_demo/scripts`目录下的其他控制节点文件，即可实现其他控制方法。

## 4 多机控制launch文件

1.多机控制launch文件为`multi_demo/launch/6_leader_follower.launch`:
```
<launch>
	<arg name="amount" value="6"/>

	<include file="$(find uav_controller)/launch/get_local_pose.launch">
		<arg name="uav_type" value="standard_vtol"/>
		<arg name="uav_amount" value="$(arg amount)"/>
	</include>

	<include file="$(find multi_demo)/launch/leader.launch">
		<arg name="uav_id" value="0"/>
	</include>
	
	<include file="$(find multi_demo)/launch/single_follower.launch">
		<arg name="uav_id" value="1"/>
		<arg name="uav_amount" value="$(arg amount)"/>
	</include>

	<include file="$(find multi_demo)/launch/single_follower.launch">
		<arg name="uav_id" value="2"/>
		<arg name="uav_amount" value="$(arg amount)"/>
	</include>
	
	<include file="$(find multi_demo)/launch/single_follower.launch">
		<arg name="uav_id" value="3"/>
		<arg name="uav_amount" value="$(arg amount)"/>
	</include>
	
	<include file="$(find multi_demo)/launch/single_follower.launch">
		<arg name="uav_id" value="4"/>
		<arg name="uav_amount" value="$(arg amount)"/>
	</include>
	
	<include file="$(find multi_demo)/launch/single_follower.launch">
		<arg name="uav_id" value="5"/>
		<arg name="uav_amount" value="$(arg amount)"/>
	</include>

</launch>
```
2.启动`get_local_pose.launch`文件，作用是统一多机编队坐标系；  
3.启动1架leader，5架follwer编队控制；  
4.若要增加或修改架次，根据`multi_demo/launch/6_leader_follower.launch`添加或删减follower即可；同时也要修改px4 launch文件中`6_vtol.launch`架次。