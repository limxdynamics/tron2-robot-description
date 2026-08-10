# 中文 | [English](README.md)

# TRON2 机器人描述

LimX TRON2A 的 URDF/Xacro、MuJoCo XML、网格和 USD 模型 — 6 种变体（双臂、双足、轮足），支持 ROS 1/2 和仿真。

## 可用变体

| 变体 | 描述 |
|------|------|
| `SF_TRON2A` | 标准双足 |
| `WF_TRON2A` | 轮足 |
| `SF_TRON2A_DUAL_ARM` | 双足 + 双臂 |
| `WF_TRON2A_DUAL_ARM` | 轮足 + 双臂 |
| `SF_TRON2A_SINGLE_ARM` | 双足 + 单臂 |
| `WF_TRON2A_SINGLE_ARM` | 轮足 + 单臂 |

## 快速开始

```bash
cd ~/limx_ws/src
git clone https://github.com/limxdynamics/tron2-robot-description.git
cd ~/limx_ws
catkin_make install  # ROS 1
# 或
colcon build  # ROS 2
```

## 相关仓库

- [tron2_mujoco_sim](https://github.com/limxdynamics/tron2_mujoco_sim) — TRON2A MuJoCo 仿真
- [tron2_gazebo_ros](https://github.com/limxdynamics/tron2_gazebo_ros) — TRON2 Gazebo 仿真
