# 仿真demo快速上手

## 1 简介

- 包含三个ros功能包：uav_control、single_demo、multi_demo;
- uav_coontrol提供了无人机控制类模板;
- single_demo为单无人机控制；
- multi_demo为多机编队控制；

## 2 使用
- 创建你自己的工作空间和功能包名；
- 将提供的三个功能包内的launch文件和scripts文件放入对应文件夹内即可；
- catkin_make编译工作空间；
- 将px4_launch文件夹内的launch文件复制到PX4-Autopilot/launch目录下。


## 3 单机控制
1.启动px4软件在环仿真环境：
启动单固定翼无人机：
```shell
roslaunch px4 single_plane.launch
```
启动单垂起固定翼无人机
```shell
roslaunch px4 single_vtol.launch
```
2.启动单机控制文件：
固定翼跟踪控制
```shell
roslaunch single_demo plane_circle_track.launch
```
垂起固定翼跟踪控制
```shell
roslaunch single_demo plane_circle_track.launch
```

## 4 多机编队

1.若地面站切换到哪个飞机才起飞，就在PX4-Autopilot/build/px4_sitl_default/etc/init.d-posix/px4-rc.params 文件中添加一行：
```shell
param set-default COM_RCL_EXCEPT 4
```
该操作为设置无遥控信号情况下，飞机仍然可以进入offboard模式。

2.启动px4软件在环环境：
````shell
roslaunch px4 6_vtol.launch 
````
3.启动多机编队launch文件：
```shell
roslaunch leader_follower_vtol 6_swarm.launch 
```
4.若要修改架次，修改对应launch文件中的无人机数量即可，最多不超过10架。（后续更多架次待开发）
