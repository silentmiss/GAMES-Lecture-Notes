# 2D transformation

## Scale
$$
\begin{rcases} x' &= s_x x \\ y' &= s_y y \end{rcases}⇒
\begin{bmatrix} x' \\ y' \end{bmatrix} = \begin{bmatrix} s_x & 0 \\ 0 & s_y \end{bmatrix} \begin{bmatrix} x \\ y \end{bmatrix}
$$

### Reflection Matrix

$$
\begin{rcases} x' &= - x \\ y' &=  y \end{rcases}⇒
\begin{bmatrix} x' \\ y' \end{bmatrix} = \begin{bmatrix} -1 & 0 \\ 0 & 1 \end{bmatrix} \begin{bmatrix} x \\ y \end{bmatrix}
$$

### Shear Matrix 切变

Exemple:
$$
\begin{rcases} x' &= x + ay \\ y' &=  y \end{rcases}⇒
\begin{bmatrix} x' \\ y' \end{bmatrix} = \begin{bmatrix} 1 & a \\ 0 & 1 \end{bmatrix} \begin{bmatrix} x \\ y \end{bmatrix}
$$

## Rotate

旋转点，旋转方向

以原点为旋转点，进行逆时针旋转：
$$
R_\theta = \begin{bmatrix} \cos\theta & -\sin\theta \\ \sin\theta & \cos\theta \end{bmatrix}
$$
（可以通过特殊点进行逆推）

## Linear Transforms = Matrices

$$
\begin{rcases} x' &= ax + by \\ y' &= cx + dy \end{rcases}⇒
\begin{bmatrix} x' \\ y' \end{bmatrix} = \begin{bmatrix} a & b \\ c & d \end{bmatrix} \begin{bmatrix} x \\ y \end{bmatrix}⇒
\mathbf{x}' = \mathbf{M} \mathbf{x} 
$$

## Translation - Affine Transformations
Homogeneous coordinates（齐次坐标）的解决办法
Translation is NOT linear transform.

$$
\begin{rcases} x' &= x + t_x \\ y' &= y + t_y \end{rcases}⇒
\begin{bmatrix} x' \\ y' \\ w' \end{bmatrix} = \begin{bmatrix} 1 & 0 & t_x \\ 0 & 1 & t_y \\ 0 & 0 & 1\end{bmatrix} \begin{bmatrix} x \\ y \\ 1 \end{bmatrix}
$$

## Inverse Transform

$$M^{-1}$$

## Composite Transforms/Composing Transforms 复合变换
= 矩阵乘法

Transform Ordering Matters!  (Matrix multiplication is NOT commutative.) 
Matrices are applied right to left. 

# 3D Transforms 
使用二维变换进行类比

