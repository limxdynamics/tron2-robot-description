# 中文 | [English](README.md)

<!--
  SPDX-FileCopyrightText: Copyright (c) 2025-2026 LimX Dynamics Technology Co., Ltd.
  SPDX-License-Identifier: Apache-2.0
-->

# TRON2 机器人描述

LimX TRON2A 的 URDF/Xacro、MuJoCo XML、网格和 USD 模型 — 6 种变体（双臂、双足、轮足），支持 ROS 1/2 和仿真。

## 可用变体

| 变体 | 描述 | ROS 1 | ROS 2 |
|------|------|-------|-------|
| `SF_TRON2A` | 标准双足 | ✅ | ✅ |
| `WF_TRON2A` | 轮足 | ✅ | ✅ |
| `SF_TRON2A_DUAL_ARM` | 双足 + 双臂 | ✅ | ✅ |
| `WF_TRON2A_DUAL_ARM` | 轮足 + 双臂 | ✅ | ✅ |
| `SF_TRON2A_SINGLE_ARM` | 双足 + 单臂 | ✅ | ✅ |
| `WF_TRON2A_SINGLE_ARM` | 轮足 + 单臂 | ✅ | ✅ |

## 目录结构

每个变体目录（如 `SF_TRON2A/`）包含：

| 目录/文件 | 内容 |
|-----------|------|
| `urdf/` | URDF 机器人描述文件 |
| `xacro/` | Xacro 宏文件 |
| `xml/` | MuJoCo MJCF XML 模型 |
| `meshes/` | STL/Collada 网格文件 |
| `usd/` | USD 通用场景描述文件（Isaac Sim 兼容） |
| `CMakeLists.txt` | ROS 包构建文件 |
| `package.xml` | ROS 包清单 |

## 快速开始

### ROS 1 (Noetic)

```bash
cd ~/limx_ws/src
git clone https://github.com/limxdynamics/tron2-robot-description.git
cd ~/limx_ws
source /opt/ros/noetic/setup.bash
catkin_make install
source install/setup.bash

# 查看可用型号
echo 'export ROBOT_TYPE=SF_TRON2A' >> ~/.bashrc && source ~/.bashrc

# 加载 URDF
rosrun xacro xacro $(rospack find tron2_description)/SF_TRON2A/xacro/robot.xacro
```

### ROS 2 (Humble/Iron)

```bash
cd ~/limx_ws/src
git clone https://github.com/limxdynamics/tron2-robot-description.git
cd ~/limx_ws
source /opt/ros/humble/setup.bash  # 或 /opt/ros/iron/setup.bash
colcon build --cmake-args -DCMAKE_BUILD_TYPE=Release
source install/setup.bash
```

### MuJoCo

```python
import mujoco
model = mujoco.MjModel.from_xml_path(
    "tron2-robot-description/SF_TRON2A/xml/robot.xml"
)
data = mujoco.MjData(model)
```

### Isaac Sim

从 `SF_TRON2A/usd/` 或对应变体目录导入 USD 文件。

## 许可证

Apache License 2.0 — 详见 [LICENSE](LICENSE)。

## 相关仓库

| 仓库 | 描述 |
|------|------|
| [tron2_mujoco_sim](https://github.com/limxdynamics/tron2_mujoco_sim) | TRON2A MuJoCo 仿真 |
| [tron2_gazebo_ros](https://github.com/limxdynamics/tron2_gazebo_ros) | TRON2 Gazebo 仿真（ROS Noetic） |
| [tron2_rl_lab](https://github.com/limxdynamics/tron2_rl_lab) | TRON2A Isaac Lab RL 训练 |
| [tron2_rl_deploy_ros](https://github.com/limxdynamics/tron2_rl_deploy_ros) | TRON2 RL 部署（ROS） |
| [tron2_rl_deploy_python](https://github.com/limxdynamics/tron2_rl_deploy_python) | TRON2 RL 部署（Python） |
| [tron2_openpi](https://github.com/limxdynamics/tron2_openpi) | TRON2 VLA 部署 |

> 如果这些模型对你有帮助，欢迎给仓库点个 Star。
