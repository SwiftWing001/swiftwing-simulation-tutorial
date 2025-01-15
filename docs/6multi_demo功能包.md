# multi_demo功能包

## 1 功能包简介
- **multi_demo功能包**为多机编队，目前仅支持VTOL机型；
- 编队算法基于leader-follower控制； 
- leader会发布自身位置、速度状态数据，供follower订阅接受。


## 2 文件说明
- `leader.py`根据设定的航线，计算得到期望的姿态控制指令发布给uav_controller包内的vector_vtol节点；同时会发布自身的位置、速度状态数据至ROS话题；
- `follower.py`将订阅leader的位置、速度数据，根据LQR控制器计算期望的姿态控制指令。

## 3 使用方法
1.启动`px4`多无人机仿真环境
```shell
roslaunch px4 6_vtol.launch
```
2.启动`leader_follower`例程
```shell
roslaunch multi_demo 6_leader_follower.launch
```

note：  
- `6_leader_follower.launch`会启动1架领航者和5架跟随者，通过修改上述的两个launch文件可以更改无人机数量；  
- 本例程中无人机的运动控制指令通过`/control_signal/att`发布；  
- 用户可通过更改领航者的轨迹更改编队轨迹，领航者的轨迹定义在`multi_demo/scripts/leader_uav.py`中  
- 可单独启动领航者进行测试，启动指令 `roslaunch multi_demo leader.launch`