# DH Studio

Interactive Denavit-Hartenberg Robotics Engineering Platform

## Overview

DH Studio is a professional browser-based application for visualizing, analyzing, and learning robot manipulator kinematics using the Denavit–Hartenberg convention. It is designed to serve robotics researchers, university professors, graduate students, and engineering teams who need a fast and intuitive tool for robot modeling and analysis.

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
- Geometric Jacobian computation $(6 \times n)$
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

## Preset Robots

| Name | Joints | Type |
|---|---|---|
| 2R Planar | 2 | Educational |
| 3R Spatial | 3 | Educational |
| PUMA 560 | 6 | Classic industrial |
| UR5 | 6 | Collaborative |
| SCARA | 4 | Assembly (prismatic) |
| Stanford | 6 | Research (prismatic) |

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

## DH Convention

The application uses the standard (Craig) Denavit-Hartenberg convention. Each joint-link pair is described by four parameters:

| Parameter | Symbol | Description |
|---|---|---|
| Joint angle | $\theta_i$ | Rotation about $z_{i-1}$ aligning $x_{i-1}$ with $x_i$ |
| Link offset | $d_i$ | Translation along $z_{i-1}$ to the common normal |
| Link length | $a_i$ | Distance between $z_{i-1}$ and $z_i$ along the common normal |
| Twist angle | $\alpha_i$ | Rotation about $x_i$ aligning $z_{i-1}$ with $z_i$ |

The transformation between consecutive frames is the product of four elementary transforms:

$$T_{i-1,i} = R_z(\theta_i) \; T_z(d_i) \; T_x(a_i) \; R_x(\alpha_i)$$

which expands to the homogeneous matrix:

$$T_{i-1,i} = \begin{bmatrix} c\theta_i & -s\theta_i \, c\alpha_i & s\theta_i \, s\alpha_i & a_i \, c\theta_i \\ s\theta_i & c\theta_i \, c\alpha_i & -c\theta_i \, s\alpha_i & a_i \, s\theta_i \\ 0 & s\alpha_i & c\alpha_i & d_i \\ 0 & 0 & 0 & 1 \end{bmatrix}$$

## Forward Kinematics

The end-effector pose is obtained by chaining all joint transforms from base to tip:

$$T_0^n = \prod_{i=1}^{n} T_{i-1}^{\,i}(q_i)$$

where $q_i = \theta_i$ for a revolute joint and $q_i = d_i$ for a prismatic joint.

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

## File Structure

```
DH_Studio/
  index.html    Single-file application (HTML, CSS, JavaScript)
  README.md     This document
```

## Technical Notes

### Geometric Jacobian

The Jacobian maps joint velocities $\dot{q}$ to end-effector velocities $\dot{x} = J(q)\,\dot{q}$. Each column is derived from the joint axis $\hat{z}_{i-1}$ of frame $i-1$:

For a **revolute** joint $i$:

$$\mathbf{J}_i = \begin{bmatrix} \hat{z}_{i-1} \times (p_e - p_{i-1}) \\ \hat{z}_{i-1} \end{bmatrix}$$

For a **prismatic** joint $i$:

$$\mathbf{J}_i = \begin{bmatrix} \hat{z}_{i-1} \\ \mathbf{0} \end{bmatrix}$$

### Manipulability

The Yoshikawa manipulability measure is computed from the linear velocity sub-Jacobian $J_v$ (first three rows):

$$w = \sqrt{\det\!\left(J_v \, J_v^T\right)} \geq 0$$

When $w \to 0$ the robot approaches a singular configuration and loses one or more degrees of freedom in task space. The status indicator in the interface reflects three states:

| Condition | Threshold | Status |
|---|---|---|
| $w > 0.1$ | Well-conditioned | Green |
| $0 < w \leq 0.1$ | Near singularity | Yellow |
| $w \approx 0$ | Singular | Red |
