# Third-Party Notices

`robot-description` (TRON2 robot description) is distributed under the
Apache License 2.0 (see [`LICENSE`](LICENSE) and [`NOTICE`](NOTICE)).

This file lists third-party components, referenced hardware, and
per-asset provenance so downstream users can comply with all applicable
licenses and re-distribution terms.

> **Status:** items marked `⚠ TO CONFIRM` are pending sign-off from the
> hardware / product / legal owners. Do not cut a public release while
> any `⚠ TO CONFIRM` entry remains.

---

## 1. First-party assets (LimX Dynamics)

| Path | Kind | License | Notes |
|------|------|---------|-------|
| `tron2/*/urdf/*.urdf` | URDF | Apache-2.0 | Generated from `xacro/` sources. |
| `tron2/*/xacro/*.xacro` | xacro | Apache-2.0 | Hand-maintained. |
| `tron2/*/xml/*.xml` | MuJoCo model | Apache-2.0 | Hand-maintained. |
| `tron2/*/meshes/*.STL` | STL mesh | Apache-2.0 ⚠ TO CONFIRM | Exported from LimX-owned CAD. Confirm no third-party CAD is embedded (see §3). |
| `tron2/*/usd/*.usd` | USD asset | Apache-2.0 ⚠ TO CONFIRM | Same provenance question as STL. |

Generation pipeline (documented in [`ASSETS.md`](ASSETS.md)):
`SolidWorks / OnShape CAD  →  STL / USD export  →  URDF via xacro`.

---

## 2. External hardware referenced in the YG variants

The `WFYG_TRON2A` and `SFYG_TRON2A` variants **reference** the following
third-party hardware. This repository ships **geometry / collision meshes
only** for integration purposes; it does **not** ship vendor firmware,
SDKs, drivers, or precision CAD.

| Hardware | Referenced as | Vendor | Provenance of geometry | License / permission |
|----------|---------------|--------|------------------------|----------------------|
| RoboSense Fairy96 LiDAR | mesh on `transition_Link`, `radar_Link` | RoboSense | ⚠ TO CONFIRM (LimX-authored placeholder vs. vendor CAD) | ⚠ TO CONFIRM |
| AgileX Piper X arm | arm and gripper meshes on `WFYG` / `SFYG` (TRON2A and TRON2B) | AgileX Robotics | AgileX's public [`piper_ros`](https://github.com/agilexrobotics/piper_ros): `piper_x_description` (arm) and `piper_description` (gripper fingers), see §2.1. `gripper_base_Link` ⚠ TO CONFIRM | MIT (§2.1); `gripper_base_Link` ⚠ TO CONFIRM |
| AgileX Ranger Air mobile chassis | chassis, steering, wheel, camera and LiDAR meshes on `DAM_TRON2A` / `DAM_TRON2B` | AgileX Robotics | AgileX's public [`ugv_gazebo_sim`](https://github.com/agilexrobotics/ugv_gazebo_sim) (`ranger_air/ranger_air_description/meshes`), see §2.2 | BSD (§2.2) |
| Huace M722 RTK | antenna / RTK box meshes on `antenna_*_Link` | Huace Navigation | ⚠ TO CONFIRM | ⚠ TO CONFIRM |
| NVIDIA Jetson Orin NX (compute) | compute box mesh on `transition_Link` | NVIDIA | ⚠ TO CONFIRM | ⚠ TO CONFIRM |
| Intel RealSense D435 | chest camera link `d435_Link` + optical frame | Intel | ⚠ TO CONFIRM | ⚠ TO CONFIRM |

**Owner action required:** For each row above, confirm one of the
following and update this file:

- **(A)** The mesh is a LimX-authored simplified placeholder, contains
  no vendor-derived CAD, and may be re-distributed under Apache-2.0.
  → Change `⚠ TO CONFIRM` to `Apache-2.0 (LimX placeholder)`.
- **(B)** The mesh is derived from a vendor CAD file that permits
  re-distribution. → Record the source URL, the vendor's re-distribution
  terms, and any attribution requirement.
- **(C)** Neither. → Replace the mesh with an approved placeholder before
  release and update this row.


### 2.1 AgileX Piper X arm meshes (MIT)

The arm and gripper-finger meshes in `tron2a/WFYG_TRON2A`,
`tron2a/SFYG_TRON2A`, `tron2b/WFYG_TRON2B` and `tron2b/SFYG_TRON2B` come
from AgileX's public [`piper_ros`](https://github.com/agilexrobotics/piper_ros)
repository:

| Mesh in this repository | Upstream mesh | Relation |
|---|---|---|
| `meshes/arm_base_Link.STL` | `piper_x_description/meshes/base_link.STL` | Identical file |
| `meshes/arm1_Link.STL` | `piper_x_description/meshes/Link1.STL` | Same geometry, re-meshed |
| `meshes/arm2_Link.STL` | `piper_x_description/meshes/Link2.STL` | Same geometry, re-meshed |
| `meshes/arm3_Link.STL` | `piper_x_description/meshes/Link3.STL` | Same geometry, re-meshed |
| `meshes/arm4_Link.STL` | `piper_x_description/meshes/Link4.STL` | Identical file |
| `meshes/arm5_Link.STL` | `piper_x_description/meshes/Link5.STL` | Same geometry, re-meshed |
| `meshes/arm6_Link.STL` | `piper_x_description/meshes/Link6.STL` | Same geometry, re-meshed |
| `meshes/gripper1_Link.STL` | `piper_description/meshes/link7.STL` | Identical file |
| `meshes/gripper2_Link.STL` | `piper_description/meshes/link8.STL` | Identical file |

`piper_x_description` is under `src/robot_description/` on the
`humble_beta1` branch (commit `2dc30fca68cbf4e04d1d0bc15c123d026380ece7`);
`piper_description` is under `src/` on the default branch (commit
`ac41fcbcdda598f01b51cf6175ed9a24d0dacadc`).
`gripper_base_Link` matches neither and is still ⚠ TO CONFIRM.
The arms of `DA`, `DACH` and `DAM` do not use Piper meshes.

`piper_ros` is distributed under the MIT License:

```
MIT License

Copyright (c) 2024 RosenYin

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

### 2.2 AgileX Ranger Air meshes (BSD)

The chassis meshes in `tron2a/DAM_TRON2A` and `tron2b/DAM_TRON2B` come
from AgileX's public
[`ugv_gazebo_sim`](https://github.com/agilexrobotics/ugv_gazebo_sim)
repository (`ranger_air/ranger_air_description/meshes`, commit
`27633a956c845903ee630538afeb17fe70afdd84`):

| Mesh in this repository | Upstream mesh | Relation |
|---|---|---|
| `meshes/chassis_Link.STL` | `base_link.STL` | Identical file |
| `meshes/steering_{L,R}_{F,B}_Link.STL` | `dae/*_steering_link.dae` | Same geometry, converted to STL |
| `meshes/wheel_{L,R}_{F,B}_Link.STL` | `dae/*_wheel_link.dae` | Same geometry, converted to STL |
| `meshes/chassis_camera_Link.STL` | `dae/camera_link.dae` | Same geometry, converted to STL |
| `meshes/lidar_{F,B}_Link.STL` | `dae/{front,back}_lidar_link.dae` | Same geometry, converted to STL |

`ranger_air_description/package.xml` declares the package license as
`BSD`; the repository has no license file or copyright line. These
meshes are redistributed under the BSD 3-Clause terms below, with
AgileX Robotics as the copyright holder:

```
Copyright (c) AgileX Robotics. All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are met:

1. Redistributions of source code must retain the above copyright notice,
   this list of conditions and the following disclaimer.

2. Redistributions in binary form must reproduce the above copyright notice,
   this list of conditions and the following disclaimer in the documentation
   and/or other materials provided with the distribution.

3. Neither the name of the copyright holder nor the names of its
   contributors may be used to endorse or promote products derived from
   this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE
LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
POSSIBILITY OF SUCH DAMAGE.
```

---

## 3. Documentation media

| Path | Kind | Provenance | License |
|------|------|------------|---------|
| `docs/images/DA.jpg` | Photo / render | ⚠ TO CONFIRM | ⚠ TO CONFIRM |
| `docs/images/DACH.jpg` | Photo / render | ⚠ TO CONFIRM | ⚠ TO CONFIRM |
| `docs/images/WF.jpg` | Photo / render | ⚠ TO CONFIRM | ⚠ TO CONFIRM |
| `docs/images/WFYG.jpg` | Photo / render | ⚠ TO CONFIRM | ⚠ TO CONFIRM |
| `docs/images/SF_0.jpg`, `docs/images/SF_1.jpg` | Photo / render | ⚠ TO CONFIRM | ⚠ TO CONFIRM |
| `docs/images/SFYG_0.jpg`, `docs/images/SFYG_1.jpg` | Photo / render | ⚠ TO CONFIRM | ⚠ TO CONFIRM |

Before release, run:
```bash
exiftool docs/images/*.jpg | grep -iE '(gps|serial|make|model|software|author|artist|copyright)'
```
and strip anything that discloses office locations, camera serials, or
individual contributors' names, unless intentionally kept:
```bash
exiftool -all= docs/images/*.jpg
```

---

## 4. Build / runtime toolchain (not redistributed)

The following are **runtime dependencies only** — they are neither
vendored nor packaged in this repository, but downstream users need
them to consume the assets.

| Tool | Purpose | License | Where obtained |
|------|---------|---------|----------------|
| xacro | Expand `.xacro` → `.urdf` | BSD-3-Clause | ROS distribution |
| urdfdom / `check_urdf` | URDF validation | BSD-2-Clause | ROS / package manager |
| MuJoCo | Load `xml/robot.xml` | Apache-2.0 (≥ 2.1.2) | https://mujoco.org |
| Isaac Sim | Load `usd/*.usd` | NVIDIA proprietary EULA | https://developer.nvidia.com/isaac-sim |
| RoboSense driver, RealSense SDK, Piper SDK, RTK driver | Runtime on physical hardware | Various | Vendor documentation |

None of the above are bundled here; users must obtain and license them
independently.

---

## 5. What this repository does **not** include

- No control policies (`.onnx`, `.pt`, `.pth`).
- No SDK binaries (`.so`, `.dll`, `.dylib`, `.lib`, wheels).
- No factory calibration values or per-serial calibration files.
- No motion / bag / trajectory data.
- No firmware.
- No customer- or site-specific configuration.

For those, see the sibling repositories in the `limxdynamics` org.

---

## 6. Update procedure

Whenever a mesh, USD, image, or upstream reference is added or changed:

1. Update the corresponding row in this file.
2. Re-run the EXIF strip (§3) and CAD-metadata scan (`strings` on the
   first 80 bytes of every STL).
3. If the change touches an `⚠ TO CONFIRM` row, block the merge on
   written sign-off from the responsible owner.
