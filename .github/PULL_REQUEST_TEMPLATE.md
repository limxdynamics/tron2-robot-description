<!--
Thanks for contributing to robot-description!
Please fill in the sections below. Delete any that are not applicable.
-->

## Summary

<!-- One paragraph: what and why. -->

## Type of change

- [ ] `fix`     — corrects a defect in URDF / xacro / MuJoCo / mesh / USD
- [ ] `feat`    — new capability or new asset (e.g. new variant)
- [ ] `asset`   — mesh, USD, or image update only
- [ ] `docs`    — README, THIRD_PARTY_NOTICES, ASSETS, or CONTRIBUTING
- [ ] `ci`      — GitHub Actions or verification tooling
- [ ] `chore`   — repo maintenance (deps, formatting, cleanup)

## Affected variants

- [ ] `DA_TRON2A`
- [ ] `DACH_TRON2A`
- [ ] `WF_TRON2A`
- [ ] `SF_TRON2A`
- [ ] `WFYG_TRON2A`
- [ ] `SFYG_TRON2A`
- [ ] Meta / repo-wide

## Verification

Paste the output (or a summary) of the local verification steps from
`CONTRIBUTING.md#verification-before-opening-a-pr`:

```text
check_urdf: ...
mujoco load: ...
xacro expand: ...
xmllint: ...
license / TODO scan: ...
```

## Provenance & licensing

<!-- Required if the PR touches meshes, USD, or images. -->

- [ ] All new / modified STL and USD files are LimX-authored or already
      cleared for Apache-2.0 re-distribution.
- [ ] STL headers and USD metadata contain no internal paths, usernames,
      or serial numbers.
- [ ] `THIRD_PARTY_NOTICES.md` and `ASSETS.md` are up to date.
- [ ] `docs/images/*.jpg` (if changed) have been EXIF-stripped and
      reviewed for people / office / non-public product visibility.

## Excluded artifacts

- [ ] This PR does **not** add: control policies (`.onnx`, `.pt`),
      SDK binaries (`.so`, `.dll`, `.dylib`, `.lib`), factory
      calibration values, firmware, rosbags / MCAP, or customer data.

## Checklist

- [ ] xacro changes are accompanied by regenerated URDFs in the same commit.
- [ ] `CHANGELOG.md` has an entry under `## [Unreleased]`.
- [ ] All commits are DCO-signed (`git commit -s`).
- [ ] CI is expected to pass.

## Related issues

<!-- Fixes #123 / Refs #456 -->
