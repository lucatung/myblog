+++
date = 2026-03-18
title = "Calib Insight: Lightweight Camera Calibration Tool"
[params]
  math = true
+++

[**Calib Insight**](https://github.com/lucatung/calib-insight) is a lightweight, open-source camera calibration tool designed for portability and simplicity.

Unlike many calibration tools, it does not rely on heavyweight third-party image processing libraries (such as OpenCV), linear algebra libraries (such as Eigen or OpenBLAS), or optimization libraries.

Instead, the core algorithms are implemented from scratch in pure C++, including linear algebra, image processing, checkerboard detection, calibration, and optimization.

In short, Calib Insight is batteries-included and self-contained. That makes it easy to build, easy to understand, and easy to modify. It compiles cleanly across platforms and can be ported to constrained environments such as embedded systems, mobile devices, and WebAssembly targets.

This post introduces what Calib Insight can do and why it is useful for camera calibration workflows.

## Features

Calib Insight is implemented in pure C++ and currently depends only on the C++ STL and the `stb_image` module (a single-header library for image loading, planned for removal in a future version). Even with complete end-to-end calibration functionality, the full codebase is only around 5000 lines, so you can read the code quickly and change it when needed.

It currently includes the following features:

### Checkerboard Corner Detection

Calib Insight detects checkerboard corners using a Hessian-based method with polynomial fitting. The detector is fast, robust, and sub-pixel accurate, and it performs well across different checkerboard patterns and lighting conditions.

### Camera Calibration

Calib Insight estimates both intrinsic and extrinsic camera parameters from the detected checkerboard corners. It uses Zhang's method (see [my previous post](/posts/zhang-calibration/)) for initialization, then refines the result with Levenberg-Marquardt optimization to minimize reprojection error.

### Calibration Result Visualization

Calib Insight generates an interactive calibration report that includes detected corners, reprojection error distribution, estimated camera parameters, and a 3D camera pose view.

![Interactive Report](interactive-report.png)

### Undistortion

After calibration, Calib Insight generates a Python script to undistort images with the estimated camera parameters.

![Undistortion](undistort-image.png)

## TODOs

Calib Insight is still in its early stages of development. Planned improvements include:
- Support for more calibration patterns (like circle grids, AprilTag, etc.)
- Support for more camera models (like fisheye, omnidirectional, Scheimpflug, etc.)
- Improve robustness of internal algorithms (like corner detection, optimization, etc.)
- Improve performance of the algorithms
- Better documentation and examples