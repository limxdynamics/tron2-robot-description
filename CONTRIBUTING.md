# Contributing to `robot-description`

Thanks for helping improve the TRON2 robot description. This repository
holds URDF / xacro sources, MuJoCo XML, STL meshes, and USD assets. The
guidelines below aim to keep the description consistent across
simulators (Gazebo, MuJoCo, Isaac Sim) and downstream stacks.

## Table of contents

- [Ways to contribute](#ways-to-contribute)
- [Development setup](#development-setup)
- [Directory conventions](#directory-conventions)
- [Coordinate & joint conventions](#coordinate--joint-conventions)
- [Regenerating URDF from xacro](#regenerating-urdf-from-xacro)
- [Mesh & USD guidelines](#mesh--usd-guidelines)
- [Verification before opening a PR](#verification-before-opening-a-pr)
- [Commit messages](#commit-messages)
- [Pull request checklist](#pull-request-checklist)
- [Adding a new robot variant](#adding-a-new-robot-variant)
- [Sign-off (DCO)](#sign-off-dco)
- [Code of conduct](#code-of-conduct)

## Ways to contribute

- Bug reports for URDF / xacro / MuJoCo XML inconsistencies.
- Mesh simplification, collision cleanup, inertia corrections.
- Documentation, verification snippets, sim integration examples.
- New robot variants under `tron2/<VARIANT>/` — see
  [Adding a new robot variant](#adding-a-new-robot-variant).

We do **not** accept:

- Vendor CAD files that are not licensed for re-distribution.
- Calibration values, firmware, control policies, or model weights.
- Images or media that disclose office locations, individuals, or
  non-public products.

## Development setup

Prerequisites:

- ROS 1 (Noetic) **or** ROS 2 (Humble+).
- `xacro`, `urdfdom` (provides `check_urdf`), `xmllint`.
- MuJoCo ≥ 2.3 (`pip install mujoco`).
- Optional: Isaac Sim, for validating `usd/` changes.

```bash
# clone
git clone https://github.com/limx-tron2/robot-description.git
cd robot-description

# ROS 1
catkin_make -DCATKIN_WHITELIST_PACKAGES=robot_description

# ROS 2
colcon build --packages-select robot_description
```

## Directory conventions

```
tron2/<VARIANT>/
├── urdf/    # generated URDF (do not hand-edit — regenerate from xacro)
├── xacro/   # xacro sources of truth
├── xml/     # MuJoCo model (robot.xml)
├── meshes/  # STL, referenced by URDF and MuJoCo
└── usd/     # USD assets (optional per variant)
```

- File names use `PascalCase_Link.STL` for links to match URDF `<link name="…_Link">`.
- Do not commit CAD source files (`.sldprt`, `.step`, `.iges`, `.blend`).
- Do not commit editor / OS junk — see `.gitignore`.

## Coordinate & joint conventions

- **World frame:** ROS-standard, x forward, y left, z up.
- **Root link:** `base_Link`; IMU at `base_imu`.
- **Zero pose:** URDF zero equals the factory-calibrated mechanical
  zero. Do **not** bake control offsets (e.g., the 180° hip yaw
  "knees-forward" offset) into the URDF — apply them in the controller.
- **Units:** meters, kilograms, radians. Inertia is expressed in
  link-frame with the origin at the link's center of mass.

## Regenerating URDF from xacro

The URDF files under `tron2/*/urdf/` are the **generated artifact**;
edits belong in `xacro/`. To regenerate:

```bash
# ROS 1
rosrun xacro xacro tron2/WF_TRON2A/xacro/robot.xacro -o tron2/WF_TRON2A/urdf/robot.urdf

# ROS 2
ros2 run xacro xacro tron2/WF_TRON2A/xacro/robot.xacro -o tron2/WF_TRON2A/urdf/robot.urdf
```

Repeat for each variant you touched. Commit both the xacro change and
the regenerated URDF in the **same** commit.

## Mesh & USD guidelines

- **STL** must be binary format (ASCII STL is rejected by CI).
- Collision meshes go in `meshes/*_collision.STL` and should be
  simplified (target < 5k triangles per link).
- Visual meshes should not exceed **50k triangles** per link.
- STL headers must not contain internal paths or usernames. Before
  committing:

  ```bash
  find tron2 -iname "*.STL" -newer .git/HEAD | while read f; do
    head -c 80 "$f" | strings
  done
  ```

  Sanitize with any binary STL rewriter (e.g. `admesh -b`, or a small
  script that zeroes bytes 0–79 then rewrites the triangle count).

- **USD** files are ASCII; run a grep for internal metadata before
  committing:

  ```bash
  grep -rE '(customLayerData|comment = "|"author"|"owner")' tron2/*/usd/*.usd
  ```

## Verification before opening a PR

Run all of the following and paste the summary into the PR description:

```bash
# 1. URDF well-formed
for u in tron2/*/urdf/*.urdf; do check_urdf "$u"; done

# 2. MuJoCo can load the XML
for x in tron2/*/xml/*.xml; do
  python -c "import mujoco, sys; mujoco.MjModel.from_xml_path(sys.argv[1])" "$x"
done

# 3. xacro expands cleanly
for x in tron2/*/xacro/robot.xacro; do xacro "$x" > /dev/null; done

# 4. XML sanity
xmllint --noout tron2/*/urdf/*.urdf tron2/*/xml/*.xml

# 5. No unresolved TODO / proprietary / confidential markers
rg -n -i "unknown|todo|proprietary|confidential" \
   README.md THIRD_PARTY_NOTICES.md ASSETS.md
```

CI runs the same steps; local pre-checks save review round-trips.

## Commit messages

Follow Conventional Commits:

```
type(scope): short imperative summary

Longer explanation if needed.

Signed-off-by: Your Name <you@example.com>
```

`type` ∈ `feat | fix | docs | refactor | chore | ci | test | asset`.
`scope` is usually a variant folder (`WF_TRON2A`, `SFYG_TRON2A`, …) or
`meta` for repo-wide changes.

## Pull request checklist

- [ ] xacro changes are accompanied by regenerated URDFs.
- [ ] Meshes have been sanitized (STL header + USD metadata).
- [ ] Verification commands run cleanly locally.
- [ ] `THIRD_PARTY_NOTICES.md` updated if any external reference changed.
- [ ] `CHANGELOG.md` has an entry under **Unreleased**.
- [ ] No calibration values, firmware, weights, logs, bags, or media
      that discloses individuals or sites.
- [ ] DCO sign-off on every commit.

## Adding a new robot variant

1. Create `tron2/<VARIANT>/{urdf,xacro,xml,meshes,usd}/`.
2. Author the xacro; regenerate URDF; author the MuJoCo XML.
3. Add a row to `ASSETS.md` and to the variant table in `README.md`.
4. Add an entry to `THIRD_PARTY_NOTICES.md` for any new external
   hardware referenced (`⚠ TO CONFIRM` if not yet cleared).
5. Add a smoke-test case to `.github/workflows/ci.yml` (add the new
   variant to the URDF / MuJoCo lists).

## Sign-off (DCO)

We use the [Developer Certificate of Origin](https://developercertificate.org/).
Every commit must be signed off:

```bash
git commit -s -m "your message"
```

Signing off certifies that you have the right to submit the change
under the repository's license.

## Code of conduct

Be respectful and constructive. Reports to
`contact@limxdynamics.com`.
