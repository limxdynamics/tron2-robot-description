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

## [0.1.0] — TBD

First public release. Contents:

- 6 robot variants under `tron2/`:
  `DA_TRON2A`, `DACH_TRON2A`, `WF_TRON2A`, `SF_TRON2A`,
  `WFYG_TRON2A`, `SFYG_TRON2A`.
- Each variant ships URDF, xacro, MuJoCo XML, STL meshes, and USD assets.
- ROS 1 (catkin) and ROS 2 (ament_cmake) install rules via
  `package.xml` and `CMakeLists.txt`.

[Unreleased]: https://github.com/limx-tron2/robot-description/compare/v0.1.0...HEAD
[0.1.0]: https://github.com/limx-tron2/robot-description/releases/tag/v0.1.0
