+++
date = 2024-10-12
title = "Camera Calibration: Zhang's Method"
[params]
  math = true
+++

In this post, we are going to introduce a widely used camera calibration method proposed by Zhang in 2000. The method is based on the observation that the calibration object can be regarded as a plane, and thus the mapping from the world coordinate frame to the image coordinate frame can be simplified to a homography.

## Simplification of camera projection matrix P

We already know how to map a point in the world coordinate frame to a pixel in the image:

$$
\begin{equation}
\begin{aligned}
\bm{X_{\text{i}}} 
& = P\bm{X_{\text{w}}} \\
& = K
\begin{bmatrix}
  R | \bm{t}
\end{bmatrix}
\bm{X_{\text{w}}} \\
& =
K
\begin{bmatrix}
  r_{11} & r_{12} & r_{13} & t_x \\
  r_{21} & r_{22} & r_{23} & t_y \\
  r_{31} & r_{32} & r_{33} & t_z \\
\end{bmatrix}
\bm{X_{\text{w}}} \\
& =
K
\begin{bmatrix}
  \bm{r_{1}} & \bm{r_{2}} & \bm{r_{3}} & \bm{t}
\end{bmatrix}
\begin{bmatrix}
X_{\text{w}} \\ Y_{\text{w}} \\ Z_{\text{w}} \\ 1
\end{bmatrix}
\end{aligned}
\end{equation}
$$

where $\bm{X_{\text{i}}} = (x, y, 1)^T$, $\bm{X_{\text{w}}} = (X_{\text{w}}, Y_{\text{w}}, Z_{\text{w}}, 1)^T$.

Because the calibration object can be regarded as a plane, we assume $Z_{\text{w}}=0$ in $\bm{X_{\text{w}}}$. Accordingly, $\bm{r_3}$ can also be removed from the matrix. Therefore, we have

$$
\begin{equation}
\begin{aligned}
\bm{X_{\text{i}}}
& = K
\begin{bmatrix}
  \bm{r_{1}} & \bm{r_{2}} & \red{\sout{\bm{r_{3}}}} & \bm{t}
\end{bmatrix}
\begin{bmatrix}
X \\ Y \\ \red{\sout{Z}} \\ 1
\end{bmatrix}
& = K
\begin{bmatrix}
  \bm{r_{1}} & \bm{r_{2}} & \bm{t}
\end{bmatrix}
\begin{bmatrix}
X \\ Y \\ 1
\end{bmatrix}
\end{aligned}
\end{equation}
$$

We use $\tilde{M}$ to denote $(X, Y)^T$, and $M$ to denote $(X, Y, 1)^T$. Then we have

$$
\begin{equation}
H = 
K
\begin{bmatrix}
  \bm{r_{1}} & \bm{r_{2}} & \bm{t}
\end{bmatrix}
\end{equation}
$$

$$
\begin{equation}
\bm{X_{\text{i}}} = HM
\end{equation}
$$
Here, the matrix $H$ is a homography.

We are going to estimate a $3\times3$ homography. Solving $(4)$ gives an estimate of $H$.

The homography $H$ is easy to obtain by calling a function such as `cv::findHomography()`. It is worth noting that we need to identify at least 4 points because $H$ has 8 DoF, and each point provides 2 observations ($x$ and $y$).

After we have estimated $H$, we need to compute $K$ from $H$.

## Constraints on the intrinsic parameters

According to $(3)$, we have

$$
\begin{equation}
H = 
K
\begin{bmatrix}
  \bm{r_{1}} & \bm{r_{2}} & \bm{t}
\end{bmatrix} =
\begin{bmatrix}
\bm{h_1} & \bm{h_2} & \bm{h_3}
\end{bmatrix}
\end{equation}
$$

Because $\bm{r_1}$ and $\bm{r_2}$ are **orthonormal**, which means that

$$
\begin{equation}
\bm{r_1}\cdot \bm{r_2} = \bm{r_1}^T\bm{r_2} = 0
\end{equation}
$$

$$
\begin{equation}
\lVert \bm{r_1} \rVert = \lVert \bm{r_2} \rVert = 1
\end{equation}
$$

According to $(6)$, we have

$$
\begin{equation}
\begin{gather*}
\begin{bmatrix}
\bm{h_1} & \bm{h_2} & \bm{h_3}
\end{bmatrix} =
K
\begin{bmatrix}
  \bm{r_{1}} & \bm{r_{2}} & \bm{t}
\end{bmatrix}
\\
K^{-1}
\begin{bmatrix}
\bm{h_1} & \bm{h_2} & \bm{h_3}
\end{bmatrix} =
\begin{bmatrix}
  \bm{r_{1}} & \bm{r_{2}} & \bm{t}
\end{bmatrix}
\\
(K^{-1}\bm{h_1}) \cdot (K^{-1}\bm{h_2}) = (K^{-1}\bm{h_1})^T (K^{-1}\bm{h_2}) = 0
\end{gather*}
\end{equation}
$$

$$
\begin{equation}
\begin{gather*}
\blue{\bm{h_1}^T K^{-T} K^{-1}\bm{h_2} = 0}
\end{gather*}
\end{equation}
$$

According to $(7)$, we have

$$
\begin{equation}
\begin{gather*}
\lVert (K^{-1}\bm{h_1}) \rVert ^2 = \lVert (K^{-1}\bm{h_2}) \rVert ^2 = 1 \\
(K^{-1}\bm{h_1})^T (K^{-1}\bm{h_1}) = \bm{h_1}^T K^{-T} K^{-1}\bm{h_1} \\
(K^{-1}\bm{h_2})^T (K^{-1}\bm{h_2}) = \bm{h_2}^T K^{-T} K^{-1}\bm{h_2}
\end{gather*}
\end{equation}
$$

$$
\begin{equation}
\blue{\bm{h_1}^T K^{-T} K^{-1}\bm{h_1} - \bm{h_2}^T K^{-T} K^{-1}\bm{h_2} = 0}
\end{equation}
$$

The blue highlighted parts are the two basic **constraints** on the intrinsic parameters, given one homography.

We define $B$ as

$$
\begin{equation}
B = K^{-T} K^{-1}
\end{equation}
$$
which is symmetric and positive definite.

Therefore the above constraints can be written as

$$
\begin{equation}
\bm{h_1}^T \green{B} \bm{h_2} = 0
\end{equation}
$$
and

$$
\begin{equation}
\bm{h_1}^T \green{B} \bm{h_1} - \bm{h_2}^T \green{B} \bm{h_2} = 0
\end{equation}
$$
## Estimate the camera calibration matrix K

By inspecting $(12)$, we can see that it has a form from which the calibration matrix $K$ can be recovered through **Cholesky decomposition**.

$$
\begin{equation}
B = AA^T
\end{equation}
$$

where $A = K^{-T}$.

In other words, if we know $B$, we can compute $K$.

Let's estimate $B$ first.

Since $B$ is symmetric (the product of a matrix and its transpose is always a symmetric matrix),

$$
\begin{equation}
B =
\begin{bmatrix}
\blue{b_{11}} & \blue{b_{12}} & \blue{b_{13}} \\
b_{12} & \blue{b_{22}} & \blue{b_{23}} \\
b_{13} & b_{23} & \blue{b_{33}} \\
\end{bmatrix}
\end{equation}
$$

it can be written as a 6-vector:

$$
\begin{equation}
\bm{b} = (\blue{b_{11}, b_{12}, b_{13}, b_{22}, b_{23}, b_{33}})^T
\end{equation}
$$

According to $(13)$, we have

$$
\begin{equation}
\begin{split}
\bm{h_1}^T B \bm{h_2}
&=
\begin{bmatrix}
h_{11} & h_{21} & h_{31}
\end{bmatrix}
\begin{bmatrix}
b_{11} & b_{12} & b_{13} \\
b_{12} & b_{22} & b_{23} \\
b_{13} & b_{23} & b_{33} \\
\end{bmatrix}
\begin{bmatrix}
h_{12} \\ h_{22} \\ h_{32}
\end{bmatrix} \\
&=
\begin{bmatrix}
h_{11} & h_{21} & h_{31}
\end{bmatrix}
\begin{bmatrix}
h_{12}b_{11} + h_{22}b_{12} + h_{32}b_{13} \\
h_{12}b_{12} + h_{22}b_{22} + h_{32}b_{23} \\
h_{12}b_{13} + h_{22}b_{23} + h_{32}b_{33}
\end{bmatrix} \\
&=
h_{11}(h_{12}b_{11} + h_{22}b_{12} + h_{32}b_{13}) \\
&\quad + h_{21}(h_{12}b_{12} + h_{22}b_{22} + h_{32}b_{23}) \\
&\quad + h_{31}(h_{12}b_{13} + h_{22}b_{23} + h_{32}b_{33}) \\
&=
h_{11}h_{12} \blue{b_{11}} +
(h_{11}h_{22} + h_{21}h_{12}) \blue{b_{12}} \\
&\quad + (h_{31}h_{12} + h_{11}h_{32}) \blue{b_{13}} + h_{21}h_{22} \blue{b_{22}} \\
&\quad + (h_{31}h_{22} + h_{21}h_{32}) \blue{b_{23}} + h_{31}h_{32} \blue{b_{33}} \\
&= 0
\end{split}
\end{equation}
$$

Let's rewrite the above equation in a more concise way:

$$
\begin{equation}
\begin{bmatrix}
h_{11}h_{12} \\
h_{11}h_{22} + h_{21}h_{12} \\
h_{31}h_{12} + h_{11}h_{32} \\
h_{21}h_{22} \\
h_{31}h_{22} + h_{21}h_{32} \\
h_{31}h_{32} \\
\end{bmatrix}
^T
\bm{b} = 0
\end{equation}
$$

To make the equation even more concise, let us denote

$$
\begin{equation}
\bm{v_{ij}} =
\begin{bmatrix}
h_{1i}h_{1j} \\
h_{1i}h_{2j} + h_{2i}h_{1j} \\
h_{3i}h_{1j} + h_{1i}h_{3j} \\
h_{2i}h_{2j} \\
h_{3i}h_{2j} + h_{2i}h_{3j} \\
h_{3i}h_{3j}
\end{bmatrix}
\end{equation}
$$

Therefore equation $(19)$ can be written as:

$$
\begin{equation}
\bm{v_{12}}^T \bm{b} = 0
\end{equation}
$$

Similarly, according to equation $(14)$, we have

$$
\begin{equation}
\begin{split}
\bm{v_{11}}^T \bm{b} - \bm{v_{22}}^T \bm{b} = 0
\end{split}
\end{equation}
$$

Combining the above two equations,

$$
\begin{equation}
V_i = 
\begin{bmatrix}
\bm{v_{12}}^T \\
\bm{v_{11}}^T - \bm{v_{22}}^T
\end{bmatrix} \\
\end{equation}
$$

we get the following equation for **one image**:

$$
\begin{equation}
V_i \bm{b} = \bm{0}
\end{equation}
$$

Assume we have $n$ images. We can **stack** the matrices into a $2n\times6$ matrix:

$$
\begin{equation}
V =
\begin{bmatrix}
V_1 \\
V_2 \\
\cdots \\
V_n
\end{bmatrix}
\end{equation}
$$

Now we have the following linear system to solve:

$$
\begin{equation}
V \bm{b} = \bm{0}
\end{equation}
$$
Apparently, this linear system has a trivial solution $\bm{b}=\bm{0}$, which is invalid. To solve this linear system, we need to impose an additional constraint:

$$
\begin{equation}
\lVert \bm{b} \rVert = 1
\end{equation}
$$

Once we have $\bm{b}$, we can compute $B$ and then compute $K$ through Cholesky decomposition.

## Camera as a measurement device

After we have estimated the camera calibration matrix $K$, we can map a pixel in the image coordinate frame to a **ray** in the camera coordinate frame.

If we specify a **measurement plane** in the camera coordinate frame, we can further map the pixel to a point on the plane.

Say the measurement plane is defined as:

$$
AX_{\text{c}} + BY_{\text{c}} + CZ_{\text{c}} + D = 0
$$

Then we can compute the **intersection of the ray and the plane** to get the 3D coordinates of the point in the camera coordinate frame.

The back-projected ray direction can be represented as:

$$
\begin{equation}
\bm{d} = K^{-1} \bm{X_{\text{i}}}
\end{equation}
$$

so the ray can be parameterized as:

$$
\begin{equation}
\bm{X_{\text{c}}}(\lambda) = \lambda\bm{d}, \quad \lambda > 0
\end{equation}
$$

where $\lambda$ is a scalar.

Therefore, we can compute $\lambda$ by substituting the ray equation into the plane equation:

$$
\begin{equation}
A(\lambda d_x) + B(\lambda d_y) + C(\lambda d_z) + D = 0
\end{equation}
$$

$$
\begin{equation}
\lambda^* = -\frac{D}{Ad_x + Bd_y + Cd_z}
\end{equation}
$$

The intersection point can be computed as:

$$
\begin{equation}
\bm{X_{\text{c}}}^* = \bm{X_{\text{c}}}(\lambda^*) = \lambda^*\bm{d}
\end{equation}
$$

If we know the pose of the camera in the world coordinate frame with respect to the measurement plane, we can further map the point from the camera coordinate frame to the world coordinate frame.

$$
\begin{equation}
\bm{X_{\text{w}}}^* = R^T (\bm{X_{\text{c}}}^* - \bm{t})
\end{equation}
$$

In other words, we can map a 2D pixel in the image to a 3D point in the world coordinate frame, which is the basis of many applications such as 3D reconstruction and augmented reality.

## References

1. [Zhang's original paper](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/tr98-71.pdf)