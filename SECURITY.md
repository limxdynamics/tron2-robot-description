# Security Policy

## Scope

`robot-description` ships **geometry, kinematics, and simulation
description files only** — URDF / xacro, MuJoCo XML, STL meshes, and USD
assets for the TRON2A humanoid platform.

It contains **no executable control logic, no network services, no SDK
binaries, no model weights, and no calibration secrets**. The primary
security concerns for this repository are therefore:

- Inadvertent disclosure of proprietary CAD, calibration values, or
  internal infrastructure metadata baked into asset files.
- Malicious files (crafted STL / USD / xacro) that could exploit a
  downstream parser.
- Supply-chain tampering of assets between publication and consumption.

Control-path vulnerabilities belong to the deployment repositories
(`tron2-rl-deploy-python`, `tron2-rl-deploy-ros`, etc.), not here.

## Supported versions

Only the tip of the `main` branch and the most recent tagged release
receive security fixes. Older tags are provided as-is.

| Version   | Supported |
|-----------|-----------|
| `main`    | ✅        |
| Latest tag| ✅        |
| Older tags| ❌        |

## Reporting a vulnerability

**Do not** open a public issue for security reports.

Email: **contact@limxdynamics.com**
Subject prefix: `[robot-description]`

Please include:

- Affected file(s) and commit / tag.
- A minimal reproducer or proof of concept.
- Impact assessment (e.g., "STL header discloses internal path",
  "malformed xacro crashes downstream parser X").
- Your preferred disclosure timeline and contact.

We aim to acknowledge reports within **3 business days** and provide a
remediation plan or an initial mitigation within **14 calendar days**.
We support coordinated disclosure; please do not publish details until a
fix or advisory is available.

## Out of scope

- Bugs in third-party parsers (urdfdom, MuJoCo, Isaac Sim, etc.) — report
  those upstream.
- Physical safety of the robot itself — report to the deployment
  repositories or to LimX product support.
- Requests to publish calibration data or firmware — this repository
  intentionally excludes those.

## Safe harbor

Good-faith security research that follows this policy will not be
pursued legally by LimX Dynamics. Please respect user privacy, avoid
service disruption, and do not access data beyond what is necessary to
demonstrate the issue.
