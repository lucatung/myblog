+++
date = 2024-10-05
title = "Camera Calibration: World to Pixel"
[params]
  math = true
+++

In this post, we introduce how a 3D point in the world coordinate frame is mapped to pixel coordinates in an image, which is a fundamental step in camera calibration and 3D computer vision.

## Homogeneous coordinates

Before we begin, let us briefly review homogeneous coordinates.
 - a.k.a. projective coordinates
 - They describe an $n$-dimensional projective space using an $n+1$-dimensional coordinate system
 - They satisfy the equivalence relation $(x_1, x_2, \dots, x_{n+1}) \sim (\lambda x_1, \lambda x_2, \dots, \lambda x_{n+1})$, where $\lambda \in \mathbb{R}$ and $\lambda \neq 0$

## Coordinate Transformations

![Image Formation](world-camera-image-coordinates.png)

(The above figure is from [LearnOpenCV - Geometry of Image Formation](https://learnopencv.com/geometry-of-image-formation/))

Let us denote

$$
\begin{equation}
\bm{\tilde{X}_{\text{w}}} = \begin{bmatrix} X_{\text{w}} & Y_{\text{w}} & Z_{\text{w}} \end{bmatrix} ^T
\end{equation}
$$

$$
\begin{equation}
\bm{X_{\text{w}}} = \begin{bmatrix} X_{\text{w}} & Y_{\text{w}} & Z_{\text{w}} & 1 \end{bmatrix}^T
\end{equation}
$$

as the coordinates of the same point in the **world coordinate frame**,

$$
\begin{equation}
\bm{\tilde{X}_{\text{c}}} = \begin{bmatrix} X_{\text{c}} & Y_{\text{c}} & Z_{\text{c}} \end{bmatrix} ^T
\end{equation}
$$

$$
\begin{equation}
\bm{X_{\text{c}}} = \begin{bmatrix} X_{\text{c}} & Y_{\text{c}} & Z_{\text{c}} & 1 \end{bmatrix}^T
\end{equation}
$$

as the coordinates of a point in the **camera coordinate frame**,

$$
\begin{equation}
\bm{\tilde{X}_{\text{i}}} = \begin{bmatrix} x & y \end{bmatrix} ^T
\end{equation}
$$

$$
\begin{equation}
\bm{X_{\text{i}}} = \begin{bmatrix} x & y & 1 \end{bmatrix} ^T
\end{equation}
$$

as the pixel coordinates of the same point in the **image coordinate frame**,

To map points from the world coordinate frame to the image coordinate frame, we need to consider the following transformations:

- Mapping from world coordinates to camera coordinates, i.e. $\bm{X_{\text{w}}} \mapsto \bm{X_{\text{c}}}$
- Mapping from camera coordinates to pixel coordinates, i.e. $\bm{X_{\text{c}}} \mapsto \bm{X_{\text{i}}}$

### World to camera transformation

This transformation accounts for the position and orientation of the camera in the world coordinate frame. It can be represented by a $3\times3$ rotation matrix $R$ and a translation vector $\bm{t}$:

$$
\begin{equation}
\bm{\tilde{X}_{\text{c}}} =
R
\bm{\tilde{X}_{\text{w}}} + \bm{t}
\end{equation}
$$

Equivalently, in homogeneous coordinates:

$$
\begin{equation}
\bm{X_{\text{c}}} =
\begin{bmatrix}
  R & \bm{t} \\
  0 & 1
\end{bmatrix}
\bm{X_{\text{w}}}
\end{equation}
$$

### Camera to pixel transformation

This transformation maps points from the camera coordinate frame to pixel coordinates. In homogeneous coordinates, it can be represented by a $3\times4$ projection matrix $P$.

$$
\begin{equation}
\bm{X_{\text{i}}} = P \bm{X_{\text{c}}}
\end{equation}
$$

Usually we consider

$$
\begin{equation}
P=\left[ K \mid \bm{0} \right] =
\begin{bmatrix}
f_x & s & c_x & 0 \\
0 & f_y & c_y & 0 \\
0 & 0 & 1 & 0
\end{bmatrix}
\end{equation}
$$

where $K$ is an upper-triangular $3\times3$ matrix called the **intrinsic matrix**. It encodes the internal parameters of the camera, such as focal length, skew, and principal point.

For simplicity, let us assume there is no skew, so the skew factor $s$ in the projection matrix above is zero. Then the intrinsic matrix $K$ simplifies to

$$
\begin{equation}
K =
\begin{bmatrix}f_x & 0 & c_x \\
0 & f_y & c_y \\
0 & 0 & 1
\end{bmatrix}
\end{equation}
$$

Why does $K$ look like this?

Starting from the pinhole camera model, the mapping from camera coordinates to pixel coordinates is

$$
\begin{equation}
x = f_x \frac{X_{\text{c}}}{Z_{\text{c}}} + c_x
\end{equation}
$$

$$
\begin{equation}
y = f_y \frac{Y_{\text{c}}}{Z_{\text{c}}} + c_y
\end{equation}
$$

Because of the division by $Z_{\text{c}}$, this mapping is not linear in Euclidean coordinates. In homogeneous coordinates, however, it becomes linear up to scale:

$$
\begin{equation}
\begin{bmatrix}x \\ y \\ 1
\end{bmatrix} \sim
\begin{bmatrix}Z_{\text{c}}x \\ Z_{\text{c}}y \\ Z_{\text{c}}
\end{bmatrix} =
\begin{bmatrix}f_x X_{\text{c}} + c_x Z_{\text{c}} \\ f_y Y_{\text{c}} + c_y Z_{\text{c}} \\ Z_{\text{c}}
\end{bmatrix} =
\begin{bmatrix}f_x & 0 & c_x \\
0 & f_y & c_y \\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}X_{\text{c}} \\ Y_{\text{c}} \\ Z_{\text{c}}
\end{bmatrix}
\end{equation}
$$

## Camera Calibration

Camera calibration is the process of estimating $R$, $\bm{t}$, and $K$ from a set of known correspondences between world points and their projections in the image.

Next time, I will introduce one of the most popular camera calibration methods: Zhang's method.