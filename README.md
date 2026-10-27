<!--
  SPDX-FileCopyrightText: 2024-2026 LimX Dynamics Technology Co., Ltd.
  SPDX-License-Identifier: Apache-2.0
-->

<div align="center">

<img src="docs/images/render_banner.jpg" alt="TRON2 robot description: TRON2A variants rendered from the MuJoCo models" width="100%">

**English** | [中文](README_zh-CN.md)

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![CI](https://github.com/limxdynamics/tron2-robot-description/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/limxdynamics/tron2-robot-description/actions/workflows/ci.yml)
[![ROS](https://img.shields.io/badge/ROS-1%20%7C%202-22314E?logo=ros)](#ros-packages)
[![MuJoCo](https://img.shields.io/badge/MuJoCo-MJCF-0A7BBB)](https://mujoco.org/)
[![Isaac Sim](https://img.shields.io/badge/Isaac%20Sim-USD-76B900?logo=nvidia)](https://developer.nvidia.com/isaac/sim)

</div>

# TRON2 robot description

URDF / xacro, MuJoCo XML, meshes, and optional USD assets for **TRON2A** and **TRON2B** robot variants — the same eight configurations in two hardware revisions, 16 model sets in total. This package is intended for simulation, visualization, and downstream tooling (ROS 1/2, MuJoCo, Isaac Sim, and similar).

## Variants

Each image is rendered from the variant's MuJoCo model in this repository with the same camera and lighting.

| | Variant | Description | Actuated DoF | Formats |
|:-:|---|---|:-:|---|
| <img src="docs/images/render_DA_TRON2A.jpg" width="320" alt="DA_TRON2A"> | `DA_TRON2A` | Dual-arm manipulators with grippers; torso-mounted IMU frame. | 14 | URDF · Xacro · MJCF · USD |
| <img src="docs/images/render_DACH_TRON2A.jpg" width="320" alt="DACH_TRON2A"> | `DACH_TRON2A` | Dual-arm configuration **with a 2-DoF head** (yaw / pitch). | 16 | URDF · Xacro · MJCF · USD |
| <img src="docs/images/render_WF_TRON2A.jpg" width="320" alt="WF_TRON2A"> | `WF_TRON2A` | **Wheeled** leg design (knee + wheel); torso IMU. | 10 | URDF · Xacro · MJCF · USD |
| <img src="docs/images/render_SF_TRON2A.jpg" width="320" alt="SF_TRON2A"> | `SF_TRON2A` | **Sole Ankle** leg design (no wheel); torso IMU. | 10 | URDF · Xacro · MJCF · USD |
| <img src="docs/images/render_WFYG_TRON2A.jpg" width="320" alt="WFYG_TRON2A"> | `WFYG_TRON2A` | WF base plus **upper-body peripherals** (arms, grippers, mast-mounted structures). | 18 | URDF · Xacro · MJCF · USD |
| <img src="docs/images/render_SFYG_TRON2A.jpg" width="320" alt="SFYG_TRON2A"> | `SFYG_TRON2A` | SF base plus the same **upper-body peripheral** stack as `WFYG_TRON2A`. | 18 | URDF · Xacro · MJCF · USD |
| <img src="docs/images/render_DASF_TRON2A.jpg" width="320" alt="DASF_TRON2A"> | `DASF_TRON2A` | **Top-bottom combined humanoid** — `SF` ankle-pitch legs joined to a `DACH` dual-arm + 2-DoF head upper body via `transition_upper_Link`. | 26 | URDF · Xacro · MJCF · USD |
| <img src="docs/images/render_DASF2_TRON2A.jpg" width="320" alt="DASF2_TRON2A"> | `DASF2_TRON2A` | **Centaur-style** variant — two `SF`-style leg pairs (front `_F` and back `_B`) under a `DACH` dual-arm + head upper body. | 36 | URDF · MJCF · USD |

The suffix **YG** denotes the richer upper-body / mast peripheral layout (arms, hands, and accessory links—see [External sensors by variant](#external-sensors-by-variant)). The **DASF** / **DASF2** variants combine previously separate lower-body (`SF`) and upper-body (`DACH`) descriptions into a single stacked (“上下拼接”) humanoid, each with its own IMU per body segment.

### TRON2A vs TRON2B

The table above shows the **TRON2A** family under `tron2a/`. `tron2b/` holds the **TRON2B** hardware revision of the same eight configurations, with the same assemblies and file layout.

| | TRON2A (`tron2a/`) | TRON2B (`tron2b/`) |
|---|---|---|
| Variants | 8 | 8 — same list |
| Formats shipped | URDF · Xacro · MJCF · USD (`DASF2` has no xacro) | URDF · MJCF · USD — **no xacro**; URDF/MJCF are authored directly |
| STL meshes | 253 | 251 |
| USD files | 40 | 40 |
| URDF zero | real robot's factory-calibrated zero | knees-forward pose (see [Joint zero convention](#joint-zero-convention)) |
| Model mass | see [Model mass](#model-mass) | see [Model mass](#model-mass) |

The renders under `docs/images/` cover TRON2A only; use them as the layout reference for TRON2B as well (the **YG** variants carry an updated sensor kit on TRON2B). The per-file manifest in [`ASSETS.md`](ASSETS.md) enumerates every asset of both trees.

**Which tree to use:** `tron2a/` when the model has to line up with an as-shipped TRON2A robot or with the TRON2A policies trained in `tron2_rl_lab`; `tron2b/` for TRON2B hardware, or when a knees-forward rest pose is preferable for training and inspection without a 180° hip-yaw offset.

## Quick start

Preview any variant in MuJoCo, no ROS needed:

```bash
git clone https://github.com/limxdynamics/tron2-robot-description.git
cd tron2-robot-description
pip install "mujoco>=2.3"
python -m mujoco.viewer --mjcf=tron2a/DACH_TRON2A/xml/robot.xml
```

Replace `DACH_TRON2A` with any folder under `tron2a/` or `tron2b/` (for example `tron2b/DACH_TRON2B`). For `DASF2_TRON2A`, load `xml/default_pose.xml` to start from the standing keyframe.

## Repository layout

Each variant lives under `tron2a/<VARIANT>/` (TRON2A) or `tron2b/<VARIANT>/` (TRON2B):

| Path | Contents |
|------|-----------|
| `urdf/` | Generated or hand-maintained URDF |
| `xacro/` | Source xacro (macro includes, collision options) — TRON2A only |
| `xml/` | MuJoCo model (`robot.xml`) with IMU sites |
| `meshes/` | Binary STL meshes (visual + collision) referenced by the models |
| `usd/` | USD assets for Isaac Sim / Omniverse workflows |

Reference images, including the renders above, are in [`docs/images/`](docs/images/).

**Not included, by design:** control policies (`.onnx`, `.pt`, `.pth`, `.ckpt`); SDK binaries (`.so`, `.dll`, `.dylib`, `.lib`) or Python wheels; per-serial or global factory-calibration values; firmware or bootloader artifacts; motion / trajectory data (rosbags, MCAP, HDF5 captures); customer-specific or site-specific configuration. For the deployment stack, model weights, and SDK, see the other TRON2 repositories of [LimX Dynamics](https://github.com/limxdynamics).

## Joint zero convention

- **Revolute / prismatic joints:** In these models, **zero angle or zero displacement** is the joint value where the exported URDF/MuJoCo joint axis definition matches the neutral mesh pose at rest. Use this as the software zero for controllers and simulators. **No calibration values are shipped in this repository** — the URDF encodes only the nominal geometric zero.
- **Floating base:** The base is **floating** in the world (six degrees of freedom: position + orientation). MuJoCo often uses a **free joint** on the root; URDF uses a **floating** base joint where applicable. Use **`base_Link`** / the documented root link for transforms; world pose comes from simulation or your localization stack.
- **Coordinates:** typical ROS usage unless stated otherwise in your integration stack: **x** forward, **y** left, **z** up from the root link.
- **TRON2A vs TRON2B zero** (affects the `SF`-style legs of `SF`, `SFYG`, `DASF` and both leg pairs of `DASF2`):
  - **`tron2a/` (TRON2A):** Before shipment, each robot is **factory-calibrated** so joint zeros match the URDF zero; refer to maintenance procedures only after hardware service or major assembly changes. This zero is **not** the knees-forward pose used for walking control. To get the knees-forward pose, rotate each leg's **hip yaw** joint by **180°** (π rad), e.g. `proximal_yaw_L_Joint` / `proximal_yaw_R_Joint`, in your controller or state initialisation.
  - **`tron2b/` (TRON2B):** The URDF zero is defined **directly as the knees-forward pose** and does **not** match the real robot's factory zero. This intentional choice makes the model more intuitive for training and visual inspection (no mental 180°-yaw offset needed to picture the walking stance). **When deploying to real TRON2B hardware, you must apply the corresponding joint offset** (the inverse of the 180° hip-yaw rotation) to convert between the URDF/sim convention and the real robot's factory zero.

| Zero position (TRON2A nominal kinematic zero) | Knees-forward pose for control (after ~180° yaw per leg) |
| :---: | :---: |
| <img src="docs/images/render_SF_TRON2A_zero.jpg" alt="SF zero position" width="400" /> | <img src="docs/images/render_SF_TRON2A_knees_forward.jpg" alt="SF knees-forward pose" width="400" /> |

<sub>Both renders add the same small hip / knee / ankle bend so the knee direction is visible; the only difference between them is the 180° hip-yaw offset.</sub>

## External sensors by variant

Summary of **torso-mounted and mast-mounted peripherals** as represented in this repository. In every variant the IMU appears as a URDF link fixed to the root and as a MuJoCo site with quaternion / gyro / accelerometer sensors. **Gazebo** entries refer to plugins in the shipped URDF where present. Real hardware may include devices not modeled here. For **YG** variants, **Mast & auxiliary URDF (YG)** lists both model link names and on-robot hardware (all LiDAR / RTK / compute / arm-side payloads are mounted on the **arm transition bracket**, URDF link **`transition_Link`**).

| Variant | Torso IMU | RGB-D / depth (chest, D435-class) | Mast & auxiliary URDF (YG) | Notes |
|--------|-----------|-------------------------------------|------------------------------|--------|
| `DA_TRON2A` | Yes — link `base_imu` | Yes — `d435_Link` + `d435_optical_frame` (geometry only; no Gazebo depth plugin) | — | MuJoCo: quaternion / gyro / accelerometer at site `base_imu`. |
| `DACH_TRON2A` | Yes — link `base_imu` | Yes — `d435_Link` + `d435_optical_frame`; Gazebo depth camera plugin | — | Full URDF/xacro/XML/USD in this tree; 2-DoF head (`head_yaw` / `head_pitch`). MuJoCo IMU at site `base_imu`. |
| `WF_TRON2A` | Yes — link `base_imu`; Gazebo `base_imu_sensor` | Yes — `d435_Link` + `d435_optical_frame`; Gazebo depth camera plugin | — | |
| `SF_TRON2A` | Yes — same pattern as WF | Yes — same D435 Gazebo block as WF | — | |
| `WFYG_TRON2A` | Yes | Yes — same chest D435 as WF | **URDF (meshes / kinematics):** `transition_Link` (转接件), `camera_mount_Link`, `radar_Link`, `antenna_L_Link`, `antenna_R_Link`. **Physical hardware** (on `transition_Link`): LiDAR **RoboSense Fairy96** (速腾 Fairy96); dual arms **AgileX Piper X** (松灵 PiperX); RTK **Huace M722** (华测 M722); compute **NVIDIA Jetson Orin NX**. **Simulation note:** URDF provides geometry/collision only; **no** Gazebo radar/RF sensor plugins in this package. | |
| `SFYG_TRON2A` | Yes | Yes | **Same YG stack as `WFYG_TRON2A`** — same URDF link set, same hardware (Fairy96, Piper X, M722, Orin NX on `transition_Link`) and the same simulation caveats. |  |
| `DASF_TRON2A` | Two — lower body `base_imu`, upper body `upper_base_imu` | Yes — `d435_U_Link` + `d435_optical_frame_U` on the upper body | — | Stacked `SF` legs + `DACH` arms/head joined at `transition_upper_Link`; MuJoCo IMU sensors on both `base_imu` and `upper_base_imu` sites. |
| `DASF2_TRON2A` | Three — `limx_F_imu` (front legs), `limx_B_imu` (back legs), `limx_H_imu` (head/upper body) | Yes — `d435_F_Link`, `d435_B_Link`, `d435_H_Link` (front / back / head cameras) | — | Centaur-style: two `SF`-style leg pairs (`transition_middle_Link` to front legs, direct to back legs) plus `DACH` upper body via `transition_upper_Link`. |

**Legend:** “RGB-D / depth” follows the Intel RealSense **D435**-style optical frame and Gazebo `depth` sensor naming (`d435_camera_sensor`) where present. Add your own drivers or Gazebo plugins if you need simulated radar or extra cameras.

## Model mass

Total mass of the robot body in each MuJoCo model (`xml/robot.xml`), i.e. the sum of all `<inertial>` masses in the robot's kinematic tree. Mocap-only visualization bodies (`target_left_body` / `target_right_body` in `DACH_TRON2A`) are excluded.

| Variant | TRON2A | TRON2B |
|---|--:|--:|
| `WF` | 34.912 kg | 35.542 kg |
| `SF` | 34.812 kg | 35.662 kg |
| `WFYG` | 41.677 kg | 42.306 kg |
| `SFYG` | 41.577 kg | 42.426 kg |
| `DA` | 59.712 kg | 60.242 kg |
| `DACH` | 61.292 kg | 61.822 kg |
| `DASF` | 66.843 kg | 68.205 kg |
| `DASF2` | 104.675 kg | 106.875 kg |

- **URDF vs MJCF:** the URDF of the same variant is typically heavier by 0.01 kg per IMU, because each IMU is a separate `base_imu` link (0.01 kg) in URDF but a massless site in MJCF.
- **Grasper models:** `robot_grasper.*` keeps the same total mass; the lumped gripper mass is redistributed over the articulated gripper links.
- **These are simulation model values, not hardware specifications.** The physical robot includes components that are not modeled here (cabling, fasteners, optional peripherals, etc.). For the weight of a shipped robot, refer to the official [TRON 2 specifications](https://limxdynamics.com/en/products/tron2/spec) or contact our sales team.

Reproduce with MuJoCo:

```bash
python -c "import mujoco, sys; m = mujoco.MjModel.from_xml_path(sys.argv[1]); print(round(sum(m.body_subtreemass[i] for i in range(1, m.nbody) if m.body_parentid[i] == 0 and m.body_mocapid[i] < 0), 3))" tron2a/DACH_TRON2A/xml/robot.xml
```

## Variant details

Details specific to each variant that are not covered above. Paths are given for `tron2a/`; `tron2b/` uses the same layout with the `_TRON2B` suffix.

| Variant | What is specific | Typical files |
|---|---|---|
| `DA_TRON2A` | Biped with **dual arms** and **parallel grippers**, for manipulation-focused simulation and control. Arms end in `grasper_L_Link` / `grasper_R_Link` (fixed in the default `robot.urdf`). | `urdf/robot.urdf`, `xml/robot.xml` |
| `DACH_TRON2A` | Same dual-arm stack as `DA` plus a **2-DoF head** (`head_yaw_Joint`, `head_pitch_Joint`) for perception. | `urdf/robot.urdf`, `xml/robot.xml`, `usd/`, `meshes/` |
| `WF_TRON2A` | Locomotion-oriented: **powered wheels** at the ankles (`wheel_L_Link` / `wheel_R_Link`) and knee joints (`knee_*`). | `urdf/robot.urdf`, `xml/robot.xml` |
| `SF_TRON2A` | **Ankle pitch** links instead of wheels; same hip/knee topology as `WF`. | `urdf/robot.urdf`, `xml/robot.xml` |
| `WFYG_TRON2A` | `WF` base plus **dual arms**, grippers and **mast-mounted accessory geometry** (`camera_mount_Link`, nested `radar_Link`, paired `antenna_*` links). A fixed `gripper_pick` body in the MuJoCo model marks the gripper pick point for grasp planning. | `urdf/robot.urdf`, `xml/robot.xml` |
| `SFYG_TRON2A` | `SF` legs with the same **YG** upper body, mast peripherals and `gripper_pick` body as `WFYG`. | `urdf/robot.urdf`, `xml/robot.xml` |
| `DASF_TRON2A` | `SF` legs joined via `transition_upper_Link` to a `DACH`-style dual-arm + 2-DoF head upper body: a biped + dual-arm assembly stacked into a single humanoid. | `urdf/robot_rl.urdf`, `xml/robot.xml` |
| `DASF2_TRON2A` | Two `SF`-style leg pairs joined front-to-back (`front_base_Link` and `back_base_Link` via `transition_middle_Link`), with a `DACH`-style upper body on the front legs via `transition_upper_Link`. | `urdf/robot.urdf`, `xml/robot.xml`, `xml/default_pose.xml` (standing keyframe, TRON2A only) |

## ROS packages

The catkin / colcon package manifest is [`package.xml`](package.xml). The ROS package name is **`robot_description`**. Install the `tron2a` / `tron2b` asset trees into your workspace share directory via the provided [`CMakeLists.txt`](CMakeLists.txt).

**ROS 1 (Noetic):**

```bash
cd ~/catkin_ws/src
ln -s /path/to/tron2-robot-description robot_description
cd ..
catkin_make -DCATKIN_WHITELIST_PACKAGES=robot_description
source devel/setup.bash
```

**ROS 2 (Humble or newer):**

```bash
cd ~/ros2_ws/src
ln -s /path/to/tron2-robot-description robot_description
cd ..
colcon build --packages-select robot_description
source install/setup.bash
```

Assets install to `$(ros2 pkg prefix robot_description)/share/robot_description/tron2a/<VARIANT>/` and `.../tron2b/<VARIANT>/`; `CMakeLists.txt` installs both trees.

Preview a variant in Rviz:

```bash
ros2 launch urdf_tutorial display.launch.py \
  model:=$(ros2 pkg prefix robot_description)/share/robot_description/tron2a/WF_TRON2A/urdf/robot.urdf
```

## Verification

The commands below are the ones CI runs (see [`.github/workflows/ci.yml`](.github/workflows/ci.yml)) across both the `tron2a/` and `tron2b/` trees. Running them locally before opening a PR saves review round-trips.

```bash
# 1. URDF well-formed
for u in tron2a/*/urdf/*.urdf tron2b/*/urdf/*.urdf; do check_urdf "$u"; done

# 2. MuJoCo can load every XML
pip install "mujoco>=2.3"
for x in tron2a/*/xml/*.xml tron2b/*/xml/*.xml; do
  python -c "import mujoco, sys; mujoco.MjModel.from_xml_path(sys.argv[1])" "$x"
done

# 3. xacro expands cleanly (TRON2A only — TRON2B ships no xacro; needs a ROS env for $(find tron2_gazebo))
pip install xacro
for x in tron2a/*/xacro/robot.urdf.xacro; do xacro "$x" > /dev/null; done

# 4. Generic XML sanity
xmllint --noout tron2a/*/urdf/*.urdf tron2b/*/urdf/*.urdf tron2a/*/xml/*.xml tron2b/*/xml/*.xml
```

## License & attribution

This project is licensed under the **Apache License, Version 2.0** (January 2004). See the [`LICENSE`](LICENSE) file for the full text. SPDX identifier: `Apache-2.0`.

- [`NOTICE`](NOTICE) — required attribution notice.
- [`THIRD_PARTY_NOTICES.md`](THIRD_PARTY_NOTICES.md) — per-asset provenance and any external hardware referenced by the YG variants.
- [`SECURITY.md`](SECURITY.md) — how to report a vulnerability.
- [`CONTRIBUTING.md`](CONTRIBUTING.md) — regeneration workflow, mesh policy, DCO sign-off.
- [`ASSETS.md`](ASSETS.md) — asset manifest and generation pipeline.
- [`CHANGELOG.md`](CHANGELOG.md) — release notes.

## Cite & support

If you use this description in academic or public work, please cite the repository:

```
@misc{limx_tron2_robot_description_2026,
  title  = {TRON2 robot description},
  author = {LimX Dynamics},
  year   = {2026},
  howpublished = {\url{https://github.com/limxdynamics/tron2-robot-description}}
}
```

- **Bug reports, feature requests and integration questions:** [GitHub Issues](https://github.com/limxdynamics/tron2-robot-description/issues).
- **Security reports:** email `contact@limxdynamics.com`; see [`SECURITY.md`](SECURITY.md).
- **Company / commercial contact:** <https://www.limxdynamics.com>.
