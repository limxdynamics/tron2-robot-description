# Asset Manifest

This document enumerates every non-source asset in `robot-description`,
with kind, count, generation pipeline, provenance, and license status.
It is the single source of truth for `THIRD_PARTY_NOTICES.md` per-file
claims and for the CI license / provenance scans.

> **Status legend:**
> - ✅ Confirmed LimX-authored, cleared for Apache-2.0 re-distribution.
> - ⚠ TO CONFIRM — pending sign-off from the hardware / product /
>   legal owner. Blocks public release.
> - ❌ Not cleared — must be replaced or removed before release.

## 1. Summary by variant

| Variant | URDF | xacro | MuJoCo XML | STL | USD | Notes |
|---------|:----:|:-----:|:----------:|:---:|:---:|-------|
| `DA_TRON2A`   | 1 | ≥1 | 1 | 23 | 5 | Dual arms + parallel grippers. |
| `DACH_TRON2A` | 1 | ≥1 | 1 | 44 | 5 | Dual arms + 2-DoF head. |
| `WF_TRON2A`   | 1 | ≥1 | 1 | 11 | 5 | Wheeled ankles. |
| `SF_TRON2A`   | 1 | ≥1 | 1 | 11 | 5 | Ankle-pitch feet. |
| `WFYG_TRON2A` | 1 | ≥1 | 1 | 40 | 5 | WF + YG mast peripherals. |
| `SFYG_TRON2A` | 1 | ≥1 | 1 | 40 | 5 | SF + YG mast peripherals. |
| **Total**     | 6 |    | 6 | **169** | **30** | 8 JPG in `docs/images/`. |

Counts are produced by:

```bash
for v in DA_TRON2A DACH_TRON2A WF_TRON2A SF_TRON2A WFYG_TRON2A SFYG_TRON2A; do
  printf "%-15s STL:%3d USD:%3d\n" $v \
    $(find tron2/$v -iname "*.STL" | wc -l) \
    $(find tron2/$v -iname "*.usd" | wc -l)
done
```

Re-run and update the table when assets are added or removed.

## 2. Generation pipeline

```
                +---------------------+
                | LimX-owned CAD      |
                | (SolidWorks / etc.) |
                +----------+----------+
                           |
              export       |
                           v
+---------------+   +--------------+   +-----------------+
| STL (binary)  |   |  USD (ASCII) |   | xacro (authored)|
+-------+-------+   +------+-------+   +--------+--------+
        |                  |                    |
        |                  |                    | xacro -o
        |                  |                    v
        |                  |            +---------------+
        |                  |            | URDF          |
        |                  |            +-------+-------+
        |                  |                    |
        +---------+--------+--------+-----------+
                  v                 v
          Gazebo / MuJoCo   Isaac Sim / Omniverse
```

Regeneration commands live in [`CONTRIBUTING.md`](CONTRIBUTING.md).

## 3. Per-kind provenance

### 3.1 URDF (`tron2/*/urdf/robot.urdf`)

- **Provenance:** ✅ Generated from LimX-authored xacro.
- **License:** Apache-2.0.
- **Re-generation policy:** URDF must be regenerated in the same commit
  as any xacro change.

### 3.2 xacro (`tron2/*/xacro/*.xacro`)

- **Provenance:** ✅ Hand-authored by LimX.
- **License:** Apache-2.0.
- **Includes:** May include shared macros within this repo. Must not
  `<xacro:include>` files outside `tron2/`.

### 3.3 MuJoCo XML (`tron2/*/xml/robot.xml`)

- **Provenance:** ✅ Hand-authored by LimX.
- **License:** Apache-2.0.
- **Assets referenced:** STL meshes only (via `<mesh file="...STL"/>`).
- **Sensors:** IMU (quaternion / gyro / accel) at site `base_imu`; no
  network sensors, no external plugins.

### 3.4 STL meshes (`tron2/*/meshes/*.STL`) — **169 files, ⚠ TO CONFIRM**

- **Format:** binary STL required.
- **Provenance to confirm:**
  1. All meshes were exported from LimX-owned CAD (not from vendor CAD
     for AgileX Piper X, RoboSense Fairy96, Huace M722, Orin NX, or
     Intel D435).
  2. Header bytes 0–79 do not disclose internal paths, usernames, or
     serial numbers.
- **Header sanity — evidence collected 2026-07-16:** a random sample of
  10 STL files (`arm2_Link`, `arm5_Link`, `transition_Link`,
  `wheel_R_Link`, `radar_Link`, `head_yaw_Link`, `wrist_yaw_L_Link`,
  `proximal_pitch_L_Link`, `proximal_yaw_R_Link`,
  `gripper_base_Link_collision1`) had their bytes 0–79 dumped through
  `strings`. Every sample returned only the SolidWorks binary-STL
  vendor magic `TSZ#`; no user names, paths (`Users/`, `/home/`,
  `Desktop/`), CAD source filenames (`.SLDPRT`, `.step`), or serial
  numbers were found. This does **not** cover mesh geometry
  provenance (Question 1 above); it only clears the header-leak
  concern for the sampled files. Owner still needs to confirm the
  full 169-file set is LimX-authored.
- **Full verification command (must be re-run before public tag):**

  ```bash
  find tron2 -iname "*.STL" | while read f; do
    head -c 80 "$f" | strings | grep -iE '(users|home|desktop|\.step|\.sldprt|serial|internal)' && echo "  ← in $f"
  done
  ```

- **Owner:** Hardware / mechanical lead.

### 3.5 USD assets (`tron2/*/usd/*.usd`) — **30 files, ⚠ TO CONFIRM**

- **Provenance to confirm:** Same two questions as STL.
- **Metadata sanity — evidence collected 2026-07-16:** all 8 sampled
  top-level `robot.usd` and `configuration/robot_*.usd` files were
  grepped for `customLayerData`, `comment = "`, `"author"`, `"owner"`,
  `"copyright"`, `Users/`, `/home/`, `.SLDPRT`, `.step`, `creator`.
  No hits. This clears the metadata-leak concern for the sampled
  files; owner still needs to confirm mesh-geometry provenance and
  that no USD `references` / `payload` composed in third-party
  content.
- **Full verification command:**

  ```bash
  grep -rE '(customLayerData|comment = "|"author"|"owner"|"copyright"|creator|Users/|/home/|\.SLDPRT|\.step)' tron2/*/usd/*.usd
  ```

- **Owner:** Hardware / mechanical lead.

### 3.6 Documentation images (`docs/images/*.jpg`) — **8 files, ⚠ TO CONFIRM**

| File | Depicts | Provenance | EXIF sanity (2026-07-16) |
|------|---------|------------|--------------------------|
| `DA.jpg`     | DA_TRON2A overview       | ⚠ TO CONFIRM | ✅ no GPS / serial / author / software / copyright tags |
| `DACH.jpg`   | DACH_TRON2A overview     | ⚠ TO CONFIRM | ✅ same |
| `WF.jpg`     | WF_TRON2A overview       | ⚠ TO CONFIRM | ✅ same |
| `WFYG.jpg`   | WFYG_TRON2A overview     | ⚠ TO CONFIRM | ✅ same |
| `SF_0.jpg`   | SF zero pose             | ⚠ TO CONFIRM | ✅ same |
| `SF_1.jpg`   | SF knees-forward pose    | ⚠ TO CONFIRM | ✅ same |
| `SFYG_0.jpg` | SFYG zero pose           | ⚠ TO CONFIRM | ✅ same |
| `SFYG_1.jpg` | SFYG knees-forward pose  | ⚠ TO CONFIRM | ✅ same |

**EXIF evidence** collected 2026-07-16 via `PIL.ExifTags` for the tags
`GPSInfo, Make, Model, Software, Artist, Copyright, DateTime,
DateTimeOriginal, XPAuthor, XPComment, OwnerName, BodySerialNumber,
SerialNumber, HostComputer, ProcessingSoftware`. All eight files
returned no matches. The "Provenance" column remains `⚠ TO CONFIRM`
because it requires **visual content review** by the content owner
(are these renders LimX-owned? are people / office interiors /
non-public products visible?) — a check that cannot be automated.

- **Verification / cleanup:**

  ```bash
  # Inspect
  exiftool docs/images/*.jpg | grep -iE '(gps|serial|make|model|software|author|artist|copyright)'

  # Strip (destructive; commit before running)
  exiftool -all= docs/images/*.jpg
  ```

- **Owner:** Content / marketing lead.

## 4. External hardware referenced (geometry only)

See [`THIRD_PARTY_NOTICES.md`](THIRD_PARTY_NOTICES.md) §2 for the
authoritative list and per-vendor confirmations. This repo ships
**geometry / collision only**; no vendor firmware, SDK, or precision
CAD is bundled.

## 5. What is intentionally excluded

- Factory calibration values (per-serial or global offsets).
- Firmware and any bootloader artifacts.
- Control policies (`.onnx`, `.pt`, `.pth`, `.ckpt`).
- Motion data (rosbags, MCAP, HDF5 trajectory captures).
- SDK binaries (`.so`, `.dll`, `.dylib`, `.lib`), including
  `libonnxruntime`, `liblimxsdk_*`, etc.
- Customer-specific configuration.
- Screenshots or renders that include people, office interiors, or
  non-public products.

If a contribution attempts to add any of the above, the PR must be
rejected regardless of file size or apparent utility.
