# Changelog

All notable changes to `robot-description` will be documented here.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- Open-source scaffolding: `NOTICE`, `THIRD_PARTY_NOTICES.md`,
  `SECURITY.md`, `CONTRIBUTING.md`, `CHANGELOG.md`, `ASSETS.md`.
- GitHub CI workflow: URDF `check_urdf`, MuJoCo XML load smoke test,
  xacro expansion, XML lint, license / TODO scan.
- Issue templates and PR template under `.github/`.
- `README.md`: "Scope" (included vs. explicitly excluded), "Verification"
  (CI-equivalent local commands), "Cite & support" sections; SPDX header.
- `package.xml`: `<url>` (website / repository / bugtracker) and
  `<author>` metadata.

### Changed
- `<version>` bumped from `0.0.0` to `0.1.0` for first public release.
- `package.xml`: enriched `<description>`, `<maintainer>`, and `<author>`
  metadata; ROS package name kept as `robot_description` for backwards
  compatibility with existing downstream `<depend>` declarations. Rename
  to `tron2_robot_description` is tracked as a future breaking change
  and intentionally deferred.
- `.gitignore`: added ROS build dirs, editor junk, CAD source formats,
  and an explicit deny-list for run-time artifacts that must not be
  committed here (`*.onnx`, `*.pt`, `*.so`, `*.dll`, `*.whl`, `*.bag`,
  `*.mcap`, …).

### Pending owner sign-off (blocks first public tag)
- Provenance and re-distribution terms for all STL / USD meshes
  (see `⚠ TO CONFIRM` rows in `THIRD_PARTY_NOTICES.md`).
- Publication decision for external hardware model names on YG variants
  (RoboSense Fairy96, AgileX Piper X, Huace M722, Jetson Orin NX,
  Intel RealSense D435).
- EXIF strip and content review of `docs/images/*.jpg`.
- Confirmation that the "factory-calibrated zero" description and the
  180° hip-yaw "knees-forward" note are cleared for public release.

## [1.3.0] — TBD

### Added
- `README.md` / `README_zh-CN.md`: new `DASF_TRON2A` and `DASF2_TRON2A`
  Models subsections, plus "Variant overview" and "External sensors by
  variant" table rows for both — `DASF_TRON2A` is a top-bottom combined
  humanoid (`SF` legs + `DACH` dual-arm/head upper body); `DASF2_TRON2A`
  is a centaur-style variant (two `SF`-style leg pairs, front/back, plus
  a `DACH` upper body).
- `ASSETS.md`: documented the TRON2B robot family (8 variants — `DA`,
  `DACH`, `WF`, `SF`, `WFYG`, `SFYG`, `DASF`, `DASF2_TRON2B`) as
  ✅ Confirmed for public release, based on a full (non-sampled) header /
  metadata scan across all 251 STL and 40 USD files (zero hits for
  leaked paths, usernames, CAD filenames, serials, or authoring
  metadata). **Not yet pushed** — two TRON2B USD files
  (`DASF_TRON2B`/`DASF2_TRON2B` `robot_base.usd`, ~111–113 MiB) exceed
  GitHub's 100 MiB hard limit and require Git LFS or re-export before
  an actual TRON2B push.

### Changed
- `docs/images/DASF.jpg`, `DASF2.jpg`, `WFYG.jpg`, `SFYG_0.jpg`: replaced
  with manually-captured hardware screenshots (previously
  pybullet-rendered placeholders).
- README (EN/中文): rewrote the "Joint zero convention" section to
  explicitly distinguish `tron2a/` (URDF zero = real robot's
  factory-calibrated zero; knees-forward needs a 180° hip-yaw software
  offset) from `tron2b/` (URDF zero is defined directly as the
  knees-forward pose, intentionally not matching factory zero, for
  training / visualization convenience — requires an inverse offset at
  deployment).
- README (EN/中文): simplified the `SFYG_TRON2A` section to a single
  overview image with a pointer to "Joint zero convention", removed the
  "(for ATEC)" qualifier from the `WFYG_TRON2A` / `SFYG_TRON2A` headers.
- Fixed stale `tron2/` path references (left over from the
  `tron2` → `tron2a` rename) in `README.md`, `README_zh-CN.md`,
  `.github/workflows/ci.yml`, and `.github/CODEOWNERS`.

### Removed
- `docs/images/SFYG_1.jpg` (superseded by the single-image `SFYG_0.jpg`
  overview).

## [0.1.0] — TBD

First public release. Contents:

- 6 robot variants under `tron2/`:
  `DA_TRON2A`, `DACH_TRON2A`, `WF_TRON2A`, `SF_TRON2A`,
  `WFYG_TRON2A`, `SFYG_TRON2A`.
- Each variant ships URDF, xacro, MuJoCo XML, STL meshes, and USD assets.
- ROS 1 (catkin) and ROS 2 (ament_cmake) install rules via
  `package.xml` and `CMakeLists.txt`.

[Unreleased]: https://github.com/limxdynamics/tron2-robot-description/compare/v1.3.0...HEAD
[1.3.0]: https://github.com/limxdynamics/tron2-robot-description/compare/v1.2.0...v1.3.0
[0.1.0]: https://github.com/limxdynamics/tron2-robot-description/releases/tag/v0.1.0
