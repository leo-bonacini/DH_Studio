# DH Editor

Interactive Denavit–Hartenberg Robotics Engineering Platform

---

## Overview

DH Editor is a professional browser-based application for visualizing, analyzing, and learning robot manipulator kinematics using the Denavit–Hartenberg convention. It is designed to serve robotics researchers, university professors, graduate students, and engineering teams who need a fast and intuitive tool for robot modeling and analysis.

---

## Features

### 3D Visualization

Built on Three.js with physically based rendering, the viewport provides:

- Real-time robot geometry (links, joints, coordinate frames, end effector)
- Orbit controls with inertia damping
- Perspective and orthographic cameras
- Configurable grid and world frame axes
- RGB coordinate frames at every joint and at the base
- Monte Carlo workspace point cloud (toggleable)
- Shadows and ambient/directional lighting

### Robotics Engine

- Standard Denavit–Hartenberg forward kinematics
- Revolute and prismatic joint types
- Geometric Jacobian computation (6×n)
- Yoshikawa manipulability measure
- Real-time singularity detection and status reporting
- Homogeneous transformation matrices per joint

### User Interface

- Editable DH parameter table with per-joint color coding
- Live joint sliders with configurable limits
- Inspector panel with four tabs: Pose · Matrices · Jacobian · Learn
- Learn tab with MathJax-rendered equations and educational explanations
- Dark and light themes
- Collapsible left and right panels
- Undo and redo (up to 80 states)
- Export and import robot configurations as JSON
- Sinusoidal joint animation
- Toast notifications

---

## Preset Robots

| Name | Joints | Type |
|---|---|---|
| 2R Planar | 2 | Educational |
| 3R Spatial | 3 | Educational |
| PUMA 560 | 6 | Classic industrial |
| UR5 | 6 | Collaborative |
| SCARA | 4 | Assembly (prismatic) |
| Stanford | 6 | Research (prismatic) |

---

## Keyboard Shortcuts

| Key | Action |
|---|---|
| Space | Toggle animation |
| R | Reset all joint values |
| T | Toggle dark/light theme |
| F | Focus camera on robot |
| H | Toggle left panel |
| I | Toggle right panel |
| Ctrl Z | Undo |
| Ctrl Y | Redo |

---

## DH Convention

The application uses the standard (Craig) Denavit–Hartenberg convention. Each joint is described by four parameters:

| Parameter | Symbol | Description |
|---|---|---|
| Joint angle | θ | Rotation about z(i−1) aligning x(i−1) with x(i) |
| Link offset | d | Translation along z(i−1) to the common normal |
| Link length | a | Distance between z(i−1) and z(i) along the common normal |
| Twist angle | α | Rotation about x(i) aligning z(i−1) with z(i) |

The transformation between consecutive frames is:

```
T(i-1, i) = Rz(θ) · Tz(d) · Tx(a) · Rx(α)
```

---

## Usage

Open `index.html` in any modern browser. No build step or server is required. CDN resources are loaded automatically:

- Three.js r160
- MathJax 3

### Adding a Robot

1. Select a preset from the dropdown or the preset grid, or start from scratch using **+ Joint**.
2. Edit the DH parameters directly in the table (values in degrees and meters).
3. Drag the joint sliders to explore configurations.
4. Open the **Jacobian** tab to inspect manipulability in real time.
5. Export your configuration as JSON for later use.

---

## File Structure

```
dth/
  index.html    Single-file application (HTML, CSS, JavaScript)
  README.md     This document
  prompt.txt    Original design specification
```

---

## Technical Notes

The Jacobian uses the geometric (velocity) formulation. For revolute joint i:

```
Ji = [ z(i−1) × (pe − p(i−1)) ]
     [         z(i−1)          ]
```

For prismatic joint i:

```
Ji = [ z(i−1) ]
     [   0    ]
```

The manipulability measure is computed from the position-only (3×n) sub-Jacobian:

```
w = sqrt( det( Jv · Jv^T ) )
```

When w approaches zero, the robot is near a singular configuration and loses one or more degrees of freedom in task space.

---

## Browser Compatibility

Requires a browser with ES module support and import map support. Tested on:

- Chrome 112+
- Firefox 108+
- Safari 16.4+
- Edge 112+
