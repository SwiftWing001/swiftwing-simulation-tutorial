# single_demo功能包

## 1 功能包简介
- **single_demo功能包**主要提供了一些基础的单机控制演示案例；
- 该功能包内的控制演示案例主要基于GVF导航向量场来计算期望速度矢量；  
- 该功能包不直接向MAVROS话题发送控制指令，而是发布上层控制指令，对于新手入门较为友好。期望控制指令由uav_contrller功能包中的`vector_fw.py`和`vector_vtol.py`启动的节点所接收，并由其发布姿态控制指令至MAVROS话题。


## 2 文件说明
- `scripts`目录下的.py文件为五个单机跟踪控制演示；
- `gvf_lib`目录下为对应的导航向量场库文件。

## 3 使用方法
预先启动uav_controller功能包中的`vector_fw.py`节点文件。
```shell
rosrun uav_cntroller vector_fw.py
```
1.启动节点文件(以`circle_track.py`为例)
```shell
rosrun single_demo circle_track.py
```
或直接在`scripts`目录下
```shell
python circle_track.py
```

2.启动节点后，`circle_track.py`会根据gvf库，计算**期望速度控制指令**，并发布至`/control_signal/vector`话题

3.若要使用att姿态控制，将`control_type = "vel"`改为`control_type = "att"`，`vector_sp`数据为`[roll, pitch, yaw, force]`。


