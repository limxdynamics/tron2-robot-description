# TRON2 robot description

URDF / xacro, MuJoCo XML, meshes, and optional USD assets for **TRON2A** humanoid variants. This package is intended for simulation, visualization, and downstream tooling (ROS 1/2, MuJoCo, Isaac Sim, and similar).

## License

This project is licensed under the **Apache License, Version 2.0** (January 2004). See the [`LICENSE`](LICENSE) file for the full text.

SPDX identifier: `Apache-2.0`.

## Repository layout

Each hardware/software configuration lives under `tron2/<VARIANT>/`:

| Path | Contents |
|------|-----------|
| `urdf/` | Generated or hand-maintained URDF |
| `xacro/` | Source xacro (macro includes, collision options) |
| `xml/` | MuJoCo model (`robot.xml`) |
| `meshes/` | STL meshes referenced by the models |
| `usd/` | USD assets (present on some variants) |

**Note:** `DACH_TRON2A` currently ships **MuJoCo XML and meshes** only (no URDF/xacro in this tree).

Illustrations for each variant are in [`docs/images/`](docs/images/) and are embedded in the [Models](#models) section below (`DA.jpg`, `DACH.jpg`, `WF.jpg`, `WFYG.jpg`, `SF_0.jpg` / `SF_1.jpg`, `SFYG_0.jpg` / `SFYG_1.jpg`).

---

## Joint zero convention

- **Revolute / prismatic joints:** In these models, **zero angle or zero displacement** is the joint value where the exported URDF/MuJoCo joint axis definition matches the neutral mesh pose at rest. Use this as the software zero for controllers and simulators.
- **Floating base:** Where a free joint is used (MuJoCo) or a fixed transform is applied in URDF, align your estimator and odometry with the documented root frame (`base_Link` / `root` as in each model).
- **Calibration:** Hardware zero may require additional offset calibration relative to this kinematic zero; refer to your robot bring-up documentation.

Coordinate conventions follow typical ROS usage unless stated otherwise in your integration stack: **x** forward, **y** left, **z** up from the root link.

- **SF / SFYG leg morphology:** The URDF/MuJoCo **zero** pose is not always the posture used on the real robot for walking control. See [SF_TRON2A](#sf_tron2a) for **zero vs knees-forward** (knees facing forward) and the proximal-yaw 180° convention.

---

## Variant overview

| Variant folder | Short description |
|----------------|-------------------|
| `DA_TRON2A` | Dual-arm manipulators with grippers; torso-mounted IMU frame. |
| `DACH_TRON2A` | Dual-arm configuration **with a 2-DoF head** (yaw / pitch). |
| `WF_TRON2A` | **Wheeled** leg design (knee + wheel); torso IMU. |
| `SF_TRON2A` | **Sole Ankle** leg design (no wheel); torso IMU. |
| `WFYG_TRON2A` | WF base plus **upper-body peripherals** (arms, grippers, mast-mounted structures). |
| `SFYG_TRON2A` | SF base plus the same **upper-body peripheral** stack as `WFYG_TRON2A`. |

The suffix **YG** denotes the richer upper-body / mast peripheral layout (arms, hands, and accessory links—see below).

---

## External sensors by variant

Summary of **torso-mounted and mast-mounted peripherals** as represented in this repository. **Gazebo** entries refer to plugins in the shipped URDF where present; **MuJoCo** models expose IMU-related sensors on the listed sites. Real hardware may include devices not modeled here. For **YG** variants, **Mast & auxiliary URDF (YG)** lists both model link names and on-robot hardware (all LiDAR / RTK / compute / arm-side payloads are mounted on the **arm transition bracket**, URDF link **`transition_Link`**).

| Variant | Torso IMU | RGB-D / depth (chest, D435-class) | Mast & auxiliary URDF (YG) | Notes |
|--------|-----------|-------------------------------------|------------------------------|--------|
| `DA_TRON2A` | Yes — link `limx_imu` | No | — | MuJoCo: quaternion / gyro / accelerometer at site `imu`. No `d435_*` chain in URDF. |
| `DACH_TRON2A` | Yes — site `imu` (MuJoCo) | No | — | MuJoCo XML only in this tree; 2-DoF head (`head_*`) with no extra sensor blocks in XML. |
| `WF_TRON2A` | Yes — `base_imu` / `limx_imu`; Gazebo `limx_imu_sensor` | Yes — `d435_Link` + `d435_optical_frame`; Gazebo depth camera plugin | — | |
| `SF_TRON2A` | Yes — same pattern as WF | Yes — same D435 Gazebo block as WF | — | |
| `WFYG_TRON2A` | Yes | Yes — same chest D435 as WF | **URDF (meshes / kinematics):** `transition_Link` (转接件), `camera_mount_Link`, `radar_Link`, `antenna_L_Link`, `antenna_R_Link`. **Physical hardware** (on `transition_Link`): LiDAR **RoboSense Fairy96** (速腾 Fairy96); dual arms **AgileX Piper X** (松灵 PiperX); RTK **Huace M722** (华测 M722); compute **NVIDIA Jetson Orin NX**. **Simulation note:** URDF provides geometry/collision only; **no** Gazebo radar/RF sensor plugins in this package. | |
| `SFYG_TRON2A` | Yes | Yes | **Same YG stack as `WFYG_TRON2A`** — same URDF link set, same hardware (Fairy96, Piper X, M722, Orin NX on `transition_Link`) and the same simulation caveats. |  |

**Legend:** “RGB-D / depth” follows the Intel RealSense **D435**-style optical frame and Gazebo `depth` sensor naming (`d435_camera_sensor`) where present. Add your own drivers or Gazebo plugins if you need simulated radar or extra cameras.

---

## Models

### DA_TRON2A

<img src="docs/images/DA.jpg" alt="DA_TRON2A overview" width="360" />

- **Summary:** Biped with **dual arms** and **parallel grippers**; suitable for manipulation-focused simulation and control development.
- **Root / IMU:** URDF exposes link `limx_imu` fixed to `base_Link` at the torso IMU origin. MuJoCo uses site `imu` on the floating root with quaternion, gyro, and accelerometer sensors.
- **End effectors:** Left / right gripper chains include visualization sites for frame axes on the tool (`L_ee_link_*`, `R_ee_link_*` in MuJoCo).
- **Typical paths:** `tron2/DA_TRON2A/urdf/robot.urdf`, `tron2/DA_TRON2A/xml/robot.xml`.

### DACH_TRON2A

<img src="docs/images/DACH.jpg" alt="DACH_TRON2A overview" width="360" />

- **Summary:** Same dual-arm stack as **DA** (`head_yaw_Joint`, `head_pitch_Joint`) for perception.
- **Root / IMU:** Same IMU modeling pattern as DA (MuJoCo site `imu` with orientation / gyro / accel sensors).
- **Typical paths:** `tron2/DACH_TRON2A/xml/robot.xml` (MuJoCo); meshes under `tron2/DACH_TRON2A/meshes/`.

### WF_TRON2A

<img src="docs/images/WF.jpg" alt="WF_TRON2A overview" width="360" />

- **Summary:** Locomotion-oriented variant with **powered wheels** at the ankles (`wheel_L_Link` / `wheel_R_Link`) and knee joints (`knee_*`).
- **Root / IMU:** URDF link `base_imu` fixed to `base_Link`; MuJoCo exposes `base_imu` sensors (orientation, gyro, accelerometer) tied to that site.
- **Typical paths:** `tron2/WF_TRON2A/urdf/robot.urdf`, `tron2/WF_TRON2A/xml/robot.xml`.

### SF_TRON2A

- **Summary:** Leg design with **ankle pitch** links instead of wheels; otherwise shares the same leg topology style as WF at the hip/knee level.
- **Root / IMU:** Same `base_imu` / MuJoCo sensor layout as **WF_TRON2A**.
- **Zero position vs control (knees-forward):** The mesh/URDF **zero** matches the standing pose in the left figure. For whole-body control we usually want **knees-forward** orientation—the knees point forward (human-like stance). In software, that layout is obtained by rotating each leg’s **hip yaw** joint by **180°** (π rad), e.g. `proximal_yaw_L_Joint` and `proximal_yaw_R_Joint`, as shown on the right. The model file still encodes the nominal zero; apply the offset in your controller or state initialisation as needed.

| Zero position (nominal kinematic zero) | Knees-forward pose for control (after ~180° yaw per leg) |
| --- | --- |
| <img src="docs/images/SF_0.jpg" alt="SF zero position" width="300" /> | <img src="docs/images/SF_1.jpg" alt="SF knees-forward pose" width="300" /> |

- **Typical paths:** `tron2/SF_TRON2A/urdf/robot.urdf`, `tron2/SF_TRON2A/xml/robot.xml`.

### WFYG_TRON2A

<img src="docs/images/WFYG.jpg" alt="WFYG_TRON2A overview" width="360" />

- **Summary:** **WF** locomotion base plus **dual arms**, grippers, and **mast-mounted accessory geometry**: `camera_mount_Link`, nested `radar_Link`, and paired `antenna_*` links (visual/collision meshes for integration with perception stacks).
- **Root / IMU:** Same torso IMU pattern as WF (`base_imu` in URDF; MuJoCo IMU sensors on `base_imu`).
- **Hardware / mast:** Physical LiDAR, arms, RTK, compute, and mount frames are listed in [External sensors by variant](#external-sensors-by-variant); URDF uses meshes/links only unless Gazebo plugins are added locally.
- **Typical paths:** `tron2/WFYG_TRON2A/urdf/robot.urdf`, `tron2/WFYG_TRON2A/xml/robot.xml`.

### SFYG_TRON2A

- **Summary:** **SF** ankle-pitch legs with the same **YG** upper-body and mast peripheral layout as `WFYG_TRON2A`.
- **Legs vs SF:** The **same zero-position and knees-forward convention** as **SF_TRON2A** applies: nominal mesh zero (left) vs **knees-forward** control pose via **180° hip yaw** per leg (right). The YG variant uses the figures below (same leg semantics as `SF_0` / `SF_1`, with arms and mast peripherals).

| Zero position (nominal kinematic zero) | Knees-forward pose for control (after ~180° yaw per leg) |
| --- | --- |
| <img src="docs/images/SFYG_0.jpg" alt="SFYG zero position" width="300" /> | <img src="docs/images/SFYG_1.jpg" alt="SFYG knees-forward pose" width="300" /> |

- **Root / IMU:** Same as SF/WF YG variants (`base_imu` / MuJoCo IMU block).
- **Hardware / mast:** Same as WFYG — see [External sensors by variant](#external-sensors-by-variant).
- **Typical paths:** `tron2/SFYG_TRON2A/urdf/robot.urdf`, `tron2/SFYG_TRON2A/xml/robot.xml`.

---

## ROS packages

The catkin / colcon package manifest is [`package.xml`](package.xml). Install the `tron2` tree into your workspace share directory via the provided `CMakeLists.txt` when building under ROS 1 or ROS 2.

---

## Contributing

Contributions are welcome. Please ensure new meshes and kinematic changes stay consistent across URDF, xacro, and MuJoCo exports where applicable, and include a short note in your pull request describing which variants were updated.

When adding documentation figures, prefer descriptive filenames under `docs/images/` and keep file sizes reasonable for Git hosting.
