# uav_controller功能包

## 1 功能包简介
uav_controller功能包提供单机基础控制功能，该功能包主要功能为创建一个无人机控制类的实例化，接收上层控制指令，发送控制指令至标准mavros控制话题。

## 2 文件说明
- `vector_fw.py`和`vector_vtol.py`对应固定翼、垂起的单机控制节点；
- `get_local_pose.py`为获取无人机在gazebo中的位置并发布，这样处理是为了解决多机编队的坐标系统一的问题；
- `FW_class.py`和`VTOL_classs.py`为封装好的固定翼、垂起控制类，并在`vector_fw.py`和`vector_vtol.py`中被调用；
- `myPID.py`提供了PID控制器类，可以直接倒入库并调用；
- `useful_function`提供常用坐标系转换、姿态四元数转换等函数；
- `spawn_uav.py`仅提供无人机状态数据获取。



## 3 使用方法
1.该功能包主要功能文件为`vector_fw.py`和`vector_vtol.py`。启动节点，使用：
```shell
rosrun uav_control vector_fw.py
```
或直接在`scripts`目录下
```shell
python vector_fw.py
```

2.启动节点后，`vector_fw.py`和`vector_vtol.py`会自动订阅相关的期望速度(或姿态)控制话题；

3.外部发送指令为姿态控制指令，会将期望姿态控制指令直接发布至`/mavros/setpoint_raw/attitude`话题进行姿态控制。

4.外部发送指令为速度控制指令，会将期望速度控制指令，输入偏航控制器+TECS控制器解算，解算后得到期望姿态和推力，发布至`/mavros/setpoint_raw/attitude`话题进行姿态控制。

## 4 其他

- 如有需要其他数据话题订阅、服务请求，可以在对应的`FW_class.py`和`VTOL_class.py`中添加；
- 若需要添加额外的底层控制算法，建议在`FW_class.py`中的`VectorControlPlane`类，或`VTOL_class.py`中的`VectorControlVtol`类中添加；
- 若需要添加其他MAVROS控制话题发布，建议在`MotionControl`函数中添加。