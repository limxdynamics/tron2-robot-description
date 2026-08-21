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
| `DA_TRON2A`    | 1 | ≥1 | 1 | 23 | 5 | Dual arms + parallel grippers. |
| `DACH_TRON2A`  | 1 | ≥1 | 1 | 44 | 5 | Dual arms + 2-DoF head. |
| `WF_TRON2A`    | 1 | ≥1 | 1 | 11 | 5 | Wheeled ankles. |
| `SF_TRON2A`    | 1 | ≥1 | 1 | 11 | 5 | Ankle-pitch feet. |
| `WFYG_TRON2A`  | 1 | ≥1 | 1 | 40 | 5 | WF + YG mast peripherals. |
| `SFYG_TRON2A`  | 1 | ≥1 | 1 | 40 | 5 | SF + YG mast peripherals. |
| `DASF_TRON2A`  | 1 | ≥1 | 1 | 36 | 5 | Top-bottom combined humanoid: SF legs + DACH upper body. |
| `DASF2_TRON2A` | 1 | 0  | 1 | 48 | 5 | "Centaur" combined design: front/back leg pairs + upper body. |
| **TRON2A total** | 8 |  | 8 | **253** | **40** | 8 JPG in `docs/images/`. |
| `DA_TRON2B`    | 1 | 0  | 1 | 23 | 5 | Dual arms + parallel grippers (TRON2B hardware rev). |
| `DACH_TRON2B`  | 2 | 0  | 1 | 42 | 5 | Dual arms + 2-DoF head (TRON2B hardware rev). |
| `WF_TRON2B`    | 1 | 0  | 1 | 11 | 5 | Wheeled ankles (TRON2B hardware rev). |
| `SF_TRON2B`    | 1 | 0  | 1 | 11 | 5 | Ankle-pitch feet (TRON2B hardware rev). |
| `WFYG_TRON2B`  | 1 | 0  | 1 | 40 | 5 | WF + YG mast peripherals, updated 2B sensor kit. |
| `SFYG_TRON2B`  | 1 | 0  | 1 | 40 | 5 | SF + YG mast peripherals, updated 2B sensor kit. |
| `DASF_TRON2B`  | 1 | 0  | 1 | 36 | 5 | Top-bottom combined humanoid (TRON2B hardware rev). |
| `DASF2_TRON2B` | 1 | 0  | 1 | 48 | 5 | "Centaur" combined design (TRON2B hardware rev). |
| **TRON2B total** | 9 |  | 8 | **251** | **40** | No xacro sources shipped for this family (URDF/XML authored directly). |
| **Grand total** | 17 |  | 16 | **504** | **80** | |

Counts are produced by:

```bash
for v in tron2a/*/ tron2b/*/; do
  printf "%-16s STL:%3d USD:%3d\n" "$(basename "$v")" \
    $(find "$v" -iname "*.STL" | wc -l) \
    $(find "$v" -iname "*.usd" | wc -l)
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

### 3.1 URDF (`tron2{a,b}/*/urdf/robot.urdf`)

- **Provenance:** ✅ Generated from LimX-authored xacro (TRON2A) or
  hand-authored directly from LimX CAD/kinematics data (TRON2B, see
  §3.2 note).
- **License:** Apache-2.0.
- **Re-generation policy:** URDF must be regenerated in the same commit
  as any xacro change (TRON2A only, since TRON2B ships no xacro).

### 3.2 xacro (`tron2a/*/xacro/*.xacro`)

- **Provenance:** ✅ Hand-authored by LimX.
- **License:** Apache-2.0.
- **Includes:** May include shared macros within this repo. Must not
  `<xacro:include>` files outside `tron2a/`.
- **TRON2B note:** the TRON2B family ships no `xacro/` directory — its
  URDF/XML/USD are authored/exported directly (same as TRON2A's
  URDF/XML outputs, minus the xacro source step). This is a build
  pipeline difference, not a provenance gap.

### 3.3 MuJoCo XML (`tron2{a,b}/*/xml/robot.xml`)

- **Provenance:** ✅ Hand-authored by LimX.
- **License:** Apache-2.0.
- **Assets referenced:** STL meshes only (via `<mesh file="...STL"/>`).
- **Sensors:** IMU (quaternion / gyro / accel) at site `base_imu` (TRON2A)
  or family-specific IMU sites (TRON2B, e.g. `limx_F_imu`/`limx_B_imu`/
  `limx_H_imu` on `DASF2_TRON2B`); no network sensors, no external
  plugins in either family.

### 3.4 STL meshes (`tron2{a,b}/*/meshes/*.STL`) — **504 files total (253 TRON2A + 251 TRON2B)**

- **Format:** binary STL required.
- **Provenance to confirm:**
  1. All meshes were exported from LimX-owned CAD (not from vendor CAD
     for AgileX Piper X, RoboSense Fairy96, Huace M722, Orin NX, or
     Intel D435).
  2. Header bytes 0–79 do not disclose internal paths, usernames, or
     serial numbers.

#### TRON2A — ⚠ TO CONFIRM (253 files)

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
  full 253-file set is LimX-authored.

#### TRON2B — ✅ Confirmed (251 files, 2026-08-21)

- **Sign-off:** hardware/product owner confirmed 2026-08-21 that the
  TRON2B family is cleared for public Apache-2.0 release.
- **Header sanity — evidence collected 2026-08-21:** all 251 TRON2B
  STL files (not a sample) were scanned:

  ```bash
  find tron2b -iname "*.STL" | while read f; do
    head -c 80 "$f" | strings | grep -iE '(users|home|desktop|\.step|\.sldprt|serial|internal)' && echo "  ← in $f"
  done
  ```

  Zero matches across all 251 files — no leaked paths, usernames, CAD
  source filenames, or serial numbers in any STL header.

- **Full verification command (must be re-run before public tag):**

  ```bash
  find tron2a -iname "*.STL" | while read f; do
    head -c 80 "$f" | strings | grep -iE '(users|home|desktop|\.step|\.sldprt|serial|internal)' && echo "  ← in $f"
  done
  ```

- **Owner:** Hardware / mechanical lead.

### 3.5 USD assets (`tron2{a,b}/*/usd/*.usd`) — **80 files total (40 TRON2A + 40 TRON2B)**

- **Provenance to confirm:** Same two questions as STL.

#### TRON2A — ⚠ TO CONFIRM (40 files)

- **Metadata sanity — evidence collected 2026-07-16:** all 8 sampled
  top-level `robot.usd` and `configuration/robot_*.usd` files were
  grepped for `customLayerData`, `comment = "`, `"author"`, `"owner"`,
  `"copyright"`, `Users/`, `/home/`, `.SLDPRT`, `.step`, `creator`.
  No hits. This clears the metadata-leak concern for the sampled
  files; owner still needs to confirm mesh-geometry provenance and
  that no USD `references` / `payload` composed in third-party
  content.

#### TRON2B — ✅ Confirmed (40 files, 2026-08-21)

- **Sign-off:** hardware/product owner confirmed 2026-08-21 that the
  TRON2B family is cleared for public Apache-2.0 release.
- **Metadata sanity — evidence collected 2026-08-21:** all 40 TRON2B
  USD files (not a sample) were grepped:

  ```bash
  grep -rE '(customLayerData|comment = "|"author"|"owner"|"copyright"|creator|Users/|/home/|\.SLDPRT|\.step)' tron2b/*/usd/*.usd tron2b/*/usd/configuration/*.usd
  ```

  Zero matches — no leaked metadata in any TRON2B USD file.
- **⚠ Size note (not a license issue, but blocks a direct `git push`):**
  `tron2b/DASF_TRON2B/usd/configuration/robot_base.usd` (≈113.2 MB) and
  `tron2b/DASF2_TRON2B/usd/configuration/robot_base.usd` (≈111.6 MB)
  both exceed GitHub's 100 MB hard push limit. These must go through
  Git LFS, be re-exported/optimized to shrink below the limit, or be
  excluded, before TRON2B can actually be pushed to the public GitHub
  mirror — independent of the license clearance above.

- **Full verification command:**

  ```bash
  grep -rE '(customLayerData|comment = "|"author"|"owner"|"copyright"|creator|Users/|/home/|\.SLDPRT|\.step)' tron2a/*/usd/*.usd tron2a/*/usd/configuration/*.usd
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
