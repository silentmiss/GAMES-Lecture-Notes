```
内容大纲：
1. 向量
2. 矩阵
3. 微积分
```

==注==：包含第三节课前半部分继续讲解的数学部分

# Vector 

分为左手系（Left-Hand System）和右手系（Right-Hand System），具有长度和方向。

## Arithmetic

### Addition & Subtraction

$$
\mathbf{p} \pm \mathbf{q}
=
\begin{bmatrix}
p_x \pm q_x \\
p_y \pm q_y \\
p_z \pm q_z 
\end{bmatrix}
, 
\mathbf{p} \pm \mathbf{q} = \mathbf{q} \pm \mathbf{p} \text{（符合交换律）}
$$

### Dot Product

也叫做内积（Inner Product）。
$$\mathbf{p} \cdot \mathbf{q} = p_x q_x + p_y q_y + p_z q_z = \mathbf{p}^{\top} \mathbf{q} = ||\mathbf{p}|| ||\mathbf{q}|| \cos\theta$$
- $\mathbf{p} \cdot \mathbf{q} = \mathbf{q} \cdot \mathbf{p}$
- $\mathbf{p} \cdot (\mathbf{q} + \mathbf{r}) = \mathbf{p} \cdot \mathbf{q} + \mathbf{p} \cdot \mathbf{r}$
- $\mathbf{p} \cdot \mathbf{p} = ||\mathbf{p}||^2_2$
- 如果 $\mathbf{p} \cdot \mathbf{q} = 0$ 且 $\mathbf{p}, \mathbf{q} \neq 0$，那么 $\cos\theta  = 0$，也就是说两个向量垂直（Orthogonal）。

### Cross Product

$$\mathbf{r} = \mathbf{p} \times \mathbf{q} = \begin{bmatrix} p_y q_z - p_z q_y \\ p_z q_x - p_x q_z \\ p_x q_y - p_y q_x \end{bmatrix}$$

- $\mathbf{r} \cdot \mathbf{p} = 0$; $\mathbf{r} \cdot \mathbf{q} = 0$; $\|\mathbf{r}\| = \|\mathbf{p}\| \|\mathbf{q}\| \sin\theta$
- $\mathbf{p} \times \mathbf{q} = -\mathbf{q} \times \mathbf{p}$
- $\mathbf{p} \times (\mathbf{q} + \mathbf{r}) = \mathbf{p} \times \mathbf{q} + \mathbf{p} \times \mathbf{r}$
- 如果 $\mathbf{p} \times \mathbf{q} = \mathbf{0}$ 且 $\mathbf{p}, \mathbf{q} \neq \mathbf{0}$ 那么 $\sin\theta = 0$, 也就是说 $\mathbf{p}$ 和 $\mathbf{q}$ 是平行的。

### Vector Norm

$$
\begin{align}
||\mathbf{p}||_1 &= |p_x| + |p_y| + |p_z| & \text{（1-norm）} \\ 
||\mathbf{p}||_2 &= (p_x^2 + p_y^2 + p_z^2)^{1/2} & \text{（Euclidean norm（2-norm））} \\
||\mathbf{p}||_p &= (p_x^p + p_y^p + p_z^p)^{1/p} & \text{（p-norm）} \\
||\mathbf{p}||_\infty &= \max(|p_x|, |p_y|, |p_z|) & \text{（Infinity norm）} 
\end{align}
$$
- Distance：$||\mathbf{q} - \mathbf{p}||$
- Unit vector：$||\mathbf{p}|| = 1$
- Normalisation：$\bar{\mathbf{p}} = \frac{\mathbf{p}}{||\mathbf{p}||}$

## 向量的应用
### Linear Representation

一个向量可以表示位置、速度、力等。
E.g. 
- 位置：$\mathbf{p}(t) = \mathbf{p} + t \mathbf{v}$
- 区间（Interpolant）：$\mathbf{p}(t) = \mathbf{p} + t (\mathbf{q} - \mathbf{p}) \text{ 或 } \mathbf{p}(t) = (1 - t)\mathbf{p} + t \mathbf{q}$
	- Segment：$0 < t < 1$ 
	- Ray：$0 < t$ 
	- Line：$t \in \mathbb{R}$ 

### Particle-Line Projection

![](GAMES103-基于物理的计算机动画入门/images/Particle-Line%20Projection.png)

### Plane Representation

![](GAMES103-基于物理的计算机动画入门/images/Plane%20Representation.png)
判断点与平面的位置关系。

### Particle-Sphere Collision

![](GAMES103-基于物理的计算机动画入门/images/Particle-Sphere%20Collision.png)
判断点以直线的方式运动的时候，是否会与某个圆相碰撞以及如果发生碰撞会产生多少个交点。

### Particle-triangle Intersection

![](GAMES103-基于物理的计算机动画入门/images/Particle-triangle%20Intersection.png)

### Barycentric Coordinates & Weights

#### Triangle
Edge vector：$$\mathbf{x}_{10} = \mathbf{x}_1 - \mathbf{x}_0, \mathbf{x}_{20} = \mathbf{x}_2 - \mathbf{x}_0$$
Normal：$$\mathbf{n} = \frac{(\mathbf{x}_{10} \times \mathbf{x}_20)}{||\mathbf{x}_{10} \times \mathbf{x}_{20}||}$$
Area：$$A = \frac{||\mathbf{x}_{10}|| h}{2} = \frac{||\mathbf{x}_{10}|| ||\mathbf{x}_{20}|| \sin\theta}{2} = \frac{||\mathbf{x}_{10} \times \mathbf{x}_{20}||}{2}$$
判断一个点是在三角形内部还是外部：
![](GAMES103-基于物理的计算机动画入门/images/Triangle%20Inside%20or%20Outside.png)
![](GAMES103-基于物理的计算机动画入门/images/Triangle%20Inside.png)

**Barycentric ...**
![](GAMES103-基于物理的计算机动画入门/images/Barycentric%20-%20Triangle.png)
Barycentric weights 是的一个三角的内部点能够被插值，从而能够实现渐变颜色的渲染。

#### Tetrahedron

**Edge vectors:**
$$\mathbf{x}_{10} = \mathbf{x}_1 - \mathbf{x}_0 \quad \mathbf{x}_{20} = \mathbf{x}_2 - \mathbf{x}_0 \quad \mathbf{x}_{30} = \mathbf{x}_3 - \mathbf{x}_0$$

**Base triangle area:**
$$A = \frac{1}{2}\|\mathbf{x}_{10} \times \mathbf{x}_{20}\|$$

**Height:**
$$h = \mathbf{x}_{30} \cdot \mathbf{n} = \mathbf{x}_{30} \cdot \frac{\mathbf{x}_{10} \times \mathbf{x}_{20}}{\|\mathbf{x}_{10} \times \mathbf{x}_{20}\|}$$

**Volume:**
$$
\begin{align}
V 
& = \frac{1}{3}hA = \frac{1}{6}\mathbf{x}_{30} \cdot \mathbf{x}_{10} \times \mathbf{x}_{20} \\
& = \frac{1}{6}\begin{vmatrix} \mathbf{x}_1 & \mathbf{x}_2 & \mathbf{x}_3 & \mathbf{x}_0 \\ 1 & 1 & 1 & 1 \end{vmatrix}
\end{align}
$$
需要注意的是这里的体积也是有符号的。
![](GAMES103-基于物理的计算机动画入门/images/Tetrahedral%20Volume.png)
**Barycentric ...**
![](GAMES103-基于物理的计算机动画入门/images/Barycentric%20-%20Tetrahedron.png)

---

# Matrices

## 基础类型

Transpose：$$\mathbf{A}^\mathrm{T} = \begin{bmatrix} a_{00} & a_{10} & a_{20} \\ a_{01} & a_{11} & a_{21} \\ a_{02} & a_{12} & a_{22} \end{bmatrix}$$
Diagonal：$$\begin{bmatrix} a_{00} & \square & \square \\ \square & a_{11} & \square \\ \square & \square & a_{22} \end{bmatrix}$$
Identity：$$\mathbf{I} = \begin{bmatrix} 1 & \square & \square \\ \square & 1 & \square \\ \square & \square & 1 \end{bmatrix}$$
Symmetric：$$\mathbf{A}^\mathrm{T} = \mathbf{A}$$
## 基础性质

### Inversibility

- $\mathbf{A}^{-1}$: $\mathbf{AA}^{-1} = \mathbf{A}^{-1}\mathbf{A} = \mathbf{I}$
- $(\mathbf{AB})^{-1} = \mathbf{B}^{-1}\mathbf{A}^{-1}$

### Orthogonality

一个正交矩阵是由一系列正交单位向量构成的。
$$\mathbf{A} = \begin{bmatrix} \mathbf{a}_0 & \mathbf{a}_1 & \mathbf{a}_2 \end{bmatrix} \quad \text{such that} \quad \mathbf{a}_i^\mathrm{T}\mathbf{a}_j = \begin{cases} 1, & \text{if } i = j \\ 0, & \text{if } i \neq j \end{cases}$$

$$\mathbf{A}^\mathrm{T}\mathbf{A} = \begin{bmatrix} \mathbf{a}_0^\mathrm{T} \\ \mathbf{a}_1^\mathrm{T} \\ \mathbf{a}_2^\mathrm{T} \end{bmatrix} \begin{bmatrix} \mathbf{a}_0 & \mathbf{a}_1 & \mathbf{a}_2 \end{bmatrix} = \begin{bmatrix} \mathbf{a}_0^\mathrm{T}\mathbf{a}_0 & \mathbf{a}_0^\mathrm{T}\mathbf{a}_1 & \mathbf{a}_0^\mathrm{T}\mathbf{a}_2 \\ \mathbf{a}_1^\mathrm{T}\mathbf{a}_0 & \mathbf{a}_1^\mathrm{T}\mathbf{a}_1 & \mathbf{a}_1^\mathrm{T}\mathbf{a}_2 \\ \mathbf{a}_2^\mathrm{T}\mathbf{a}_0 & \mathbf{a}_2^\mathrm{T}\mathbf{a}_1 & \mathbf{a}_2^\mathrm{T}\mathbf{a}_2 \end{bmatrix} = \mathbf{I}$$
性质：
$$\boxed{\mathbf{A}^\mathrm{T} = \mathbf{A}^{-1}}$$

### Symmetric Positive Definiteness（s.p.d.，对称正定矩阵）

- **s.p.d. 矩阵**：矩阵 $\mathbf{A}$ 是 s.p.d. 当且仅当对任意非零向量 $\mathbf{v}$，都有 $\mathbf{v}^{\text{T}} \mathbf{A} \mathbf{v} > 0$。
- **半正定矩阵**（symmetric semi-definite）：若 $\mathbf{v}^{\text{T}} \mathbf{A} \mathbf{v} \ge 0$，则称 $\mathbf{A}$ 为对称半正定矩阵。

#### 逆矩阵性质

- s.p.d. 矩阵一定是可逆的，其逆矩阵为： $$ \mathbf{A}^{-1} = (\mathbf{U}^{\text{T}})^{-1} \mathbf{D}^{-1} \mathbf{U}^{-1} = \mathbf{U} \mathbf{D}^{-1} \mathbf{U}^{\text{T}} $$ 由于 $\mathbf{D}$ 的对角元素均为正，其逆 $\mathbf{D}^{-1}$ 也存在且对角元素为正，因此 $\mathbf{A}^{-1}$ 也是 s.p.d. 矩阵。

#### 不同的判定方法

##### 对角线判定
- 若对角矩阵 $\mathbf{D}$ 的所有对角元素 $d_i > 0$，则 $\mathbf{v}^{\text{T}} \mathbf{D} \mathbf{v} > 0$。
- 对于一般对称矩阵 $\mathbf{A}$，若其可进行特征值分解 $\mathbf{A} = \mathbf{U} \mathbf{D} \mathbf{U}^{\text{T}}$（其中 $\mathbf{U}$ 正交），则 $\mathbf{v}^{\text{T}} \mathbf{A} \mathbf{v} = (\mathbf{U}^{\text{T}} \mathbf{v})^{\text{T}} \mathbf{D} (\mathbf{U}^{\text{T}} \mathbf{v})$。由于 $\mathbf{U}$ 正交，$\mathbf{U}^{\text{T}} \mathbf{v}$ 仍是非零向量，因此只要 $\mathbf{D}$ 的所有特征值 $d_i > 0$，就能保证 $\mathbf{v}^{\text{T}} \mathbf{A} \mathbf{v} > 0$。
、
##### 特征值判定
矩阵 $\mathbf{A}$ 是 s.p.d. 当且仅当其所有特征值均为正数，即 $\mathbf{A} = \mathbf{U} \mathbf{D} \mathbf{U}^{\text{T}}$ 且 $d_0, d_1, \dots > 0$。

#### 实际检测方法

虽然特征值分解是理论上的判定标准，但计算成本高。因此，实践中常用**对角占优（Diagonally Dominant）** 条件来快速判断：
- 若矩阵 $\mathbf{A}$ 满足 $a_{ii} > \sum_{i \ne j} |a_{ij}|$ 对所有 $i$ 成立，则 $\mathbf{A}$ 是正定矩阵。

#### 常用定理

==如果 $\mathbf{A}$ 是 **对称正定的**.，那么 $\mathbf{B} = \begin{bmatrix} \mathbf{A} & -\mathbf{A} \\ -\mathbf{A} & \mathbf{A} \end{bmatrix}$ 是半对称正定。==
证明过程：
对于任意向量 **x** 和 **y**，我们有：
$$
\begin{bmatrix} \mathbf{x}^{\text{T}} & \mathbf{y}^{\text{T}} \end{bmatrix} 
\mathbf{B} 
\begin{bmatrix} \mathbf{x} \\ \mathbf{y} \end{bmatrix} 
= 
\begin{bmatrix} \mathbf{x}^{\text{T}} & \mathbf{y}^{\text{T}} \end{bmatrix} 
\begin{bmatrix} \mathbf{A} & -\mathbf{A} \\ -\mathbf{A} & \mathbf{A} \end{bmatrix} 
\begin{bmatrix} \mathbf{x} \\ \mathbf{y} \end{bmatrix} = \mathbf{x}^{\text{T}}\mathbf{A}(\mathbf{x} - \mathbf{y}) - \mathbf{y}^{\text{T}}\mathbf{A}(\mathbf{x} - \mathbf{y}) = (\mathbf{x} - \mathbf{y})^{\text{T}}\mathbf{A}(\mathbf{x} - \mathbf{y})$$
因为 $\mathbf{A}$ 是 s.p.d.，因此我们一定有：$\begin{bmatrix} \mathbf{x}^{\text{T}} & \mathbf{y}^{\text{T}} \end{bmatrix} \mathbf{B} \begin{bmatrix} \mathbf{x} \\ \mathbf{y} \end{bmatrix} \ge 0$ 

## Multiplication

| 性质     | 公式                                                                                           |
| :----- | :------------------------------------------------------------------------------------------- |
| 不满足交换律 | $\mathbf{AB} \neq \mathbf{BA}$                                                               |
| 转置的乘积  | $(\mathbf{AB})^\mathrm{T} = \mathbf{B}^\mathrm{T}\mathbf{A}^\mathrm{T}$                      |
| 单位矩阵作用 | $\mathbf{Ix} = \mathbf{x}$                                                                   |
| 结合律    | $(\mathbf{AB})\mathbf{x} = \mathbf{A}(\mathbf{Bx})$                                          |
| 对称性证明  | $(\mathbf{A}^\mathrm{T}\mathbf{A})^\mathrm{T} = \mathbf{A}^\mathrm{T}\mathbf{A}$ (symmetric) |
| 单位矩阵乘法 | $\mathbf{AI} = \mathbf{IA} = \mathbf{A}$                                                     |
**注意：** 不是所有矩阵都是可逆的, e.g., $\mathbf{A} = \begin{bmatrix} 0 & 0 & 0 \\ 0 & 0 & 0 \\ 0 & 0 & 0 \end{bmatrix}$.

## Matrix Transformation

一次转动可以被表示成一个正交矩阵。
![](GAMES103-基于物理的计算机动画入门/images/Rotation.png)
一次缩放可以被表示成一个对角矩阵。
![](GAMES103-基于物理的计算机动画入门/images/Scaling.png)

## Singular Value Decomposition（SVD，奇异值分解）

任何矩阵都可以被表示成以下形式：
$$\mathbf{A} = \mathbf{U} \mathbf{D} \mathbf{V}^\top$$
其中 $\mathbf{D}$ 是对角矩阵，$\mathbf{V}$ 和 $\mathbf{U}$ 是正交矩阵。
也就是说，任何复杂的线性变换都可以被分解成三个步骤：旋转——缩放——旋转。

## Eigenvalue Decomposition（特征值分解）

任何对称矩阵（Symmetric Matrix）都可以被表示成以下形式：
$$\mathbf{A} = \mathbf{U} \mathbf{D} \mathbf{U}^{-1}$$
其中 $\mathbf{D}$ 是对角矩阵，$\mathbf{U}$ 是正交矩阵。

同时，因为 $\mathbf{U}$ 是正交矩阵，因此可以写成 $\mathbf{A} = \mathbf{U} \mathbf{D} \mathbf{U}^\top$ 的形式。

以下是灰色方框中内容的 Markdown 转换：

特征值与特征向量的求解
对于任意一个向量 $\mathbf{U} = [\dots \ \mathbf{u}_i \ \dots]$，我们有：
$$ \mathbf{A} \mathbf{u}_i 
= \mathbf{U} \mathbf{D} \mathbf{U}^{\text{T}} \mathbf{u}_i 
= \mathbf{U} \mathbf{D} \begin{bmatrix} \vdots \\ 0 \\ 1 \\ 0 \\ \vdots \end{bmatrix} 
= \mathbf{U} \begin{bmatrix} \vdots \\ 0 \\ d_i \\ 0 \\ \vdots \end{bmatrix} = d_i \mathbf{u}_i $$
这里的 $\mathbf{u}_i$ 就是 $d_i$ 的特征向量。

当然，对于不对称的矩阵也可以做特征值分解，只不过特征值和特征向量会更加复杂。

## Linear Solver

很多数值问题都可以最后归结于解决一个线性系统的问题：$$\mathbf{A} \mathbf{x} = \mathbf{b}$$
其中 $\mathbf{A}$ 是 square matrix，$\mathbf{x}$ 是未知数，$\mathbf{b}$ 是 boundary vector。
由于计算逆矩阵十分不便且代价高昂，因此不能够直接求得未知数，所以有了以下两种解决方式：direct 和 iterative。

### Direct Linear Solver

该种求解方式主要基于 LU 分解（LU factorization），其将一个矩阵分解为一个下三角矩阵（Lower Triangle） $\mathbf{L}$ 和一个上三角矩阵（Upper Triangle） $\mathbf{U}$ 的乘积。
 $$
\mathbf{A} = 
\mathbf{L}\mathbf{U} = \begin{bmatrix} l_{00} & \square & \square \\ l_{10} & l_{11} & \square \\ \vdots & \cdots & \ddots \end{bmatrix} 
\begin{bmatrix} \ddots & \cdots & \vdots \\ \square & u_{n-1,n-1} & u_{n-1,n} \\ \square & \square & u_{n,n} \end{bmatrix}
$$
首先解决 $\mathbf{L}\mathbf{y} = \mathbf{b}$ 
$$
\begin{bmatrix} l_{00} & \square & \square \\ l_{10} & l_{11} & \square \
\vdots & \cdots & \ddots \end{bmatrix} 
\begin{bmatrix} y_0 \ y_1 \\ \vdots \end{bmatrix}
=
\begin{bmatrix} b_0 \ b_1 \ \vdots \end{bmatrix}
$$

其中： 
$$
\begin{aligned} y_0 &= b_0 / l_{00} \\ 
y_1 &= (b_1 - l_{10} y_0) / l_{11} \ \cdots \end{aligned}
$$

然后解决 $\mathbf{U}\mathbf{x} = \mathbf{y}$ 
$$
\begin{bmatrix} \ddots & \cdots & \vdots \\ \square & u_{n-1,n-1} & u_{n-1,n} \\ \square & \square & u_{n,n} \end{bmatrix} 
\begin{bmatrix} \vdots \\ x_{n-1} \ x_n \end{bmatrix}
=
\begin{bmatrix} \vdots \ y_{n-1} \ y_n \end{bmatrix}
$$

其中： 
$$
\begin{aligned} x_n &= y_n / u_{n,n} \\ 
x_{n-1} &= (y_{n-1} - u_{n-1,n} x_n) / u_{n-1,n-1} \ \cdots \end{aligned}
$$

### Iterative Linear Solver 

该种解法的形式：
$$\mathbf{x}^{[k+1]} = \mathbf{x}^{[k]} + \alpha \mathbf{M}^{-1} (\mathbf{b} - \mathbf{A}\mathbf{x}^{[k]})$$
其中：
- $\alpha$：relaxation（松弛因子）
- $\mathbf{M}$：iterative matrix（迭代矩阵）
- $\mathbf{b} - \mathbf{A}\mathbf{x}^{[k]}$：residual error（残差误差）

工作原理：
$$ \begin{aligned} \mathbf{b} - \mathbf{A}\mathbf{x}^{[k+1]} 
&= \mathbf{b} - \mathbf{A}\mathbf{x}^{[k]} - \alpha \mathbf{A}\mathbf{M}^{-1} (\mathbf{b} - \mathbf{A}\mathbf{x}^{[k]}) \\ 
&= (\mathbf{I} - \alpha \mathbf{A}\mathbf{M}^{-1})(\mathbf{b} - \mathbf{A}\mathbf{x}^{[k]}) = (\mathbf{I} - \alpha \mathbf{A}\mathbf{M}^{-1})^{k+1} (\mathbf{b} - \mathbf{A}\mathbf{x}^{[0]}) \end{aligned} $$
因此
$$ \mathbf{b} - \mathbf{A}\mathbf{x}^{[k+1]} \to 0, \text{ if } \rho(\mathbf{I} - \alpha \mathbf{A}\mathbf{M}^{-1}) < 1. $$
其中 $\rho(\mathbf{I} - \alpha \mathbf{A}\mathbf{M}^{-1})$ 是谱半径（特征值的最大绝对值）

解法技巧：$\mathbf{M}$ 的选择
Jacobi Method： 
$$ \mathbf{M} = \text{diag}(\mathbf{A}) $$ Gauss-Seidel Method： 
$$ \mathbf{M} = \text{lower}(\mathbf{A}) $$

---

# Tensor Calculator

## 1st-Order Derivatives

一维的情况：
![](GAMES103-基于物理的计算机动画入门/images/1st-Order%20Derivatives（1D）.png)

三维的情况：
![](GAMES103-基于物理的计算机动画入门/images/1st-Order%20Derivatives（3D）.png)

## 2nd-Order Derivatives

![](GAMES103-基于物理的计算机动画入门/images/2nd-Order%20Derivatives（1D）.png)

## Example: Spring

### 单端固定弹簧系统

系统能量函数定义为：$$E(\mathbf{x}) = \frac{k}{2} \left( ||\mathbf{x}|| - L \right)^2$$其中：
- $\mathbf{x}$：弹簧当前长度向量（从固定点指向自由端）
- $L$：弹簧自然长度（Rest length）
- $k$：弹簧刚度系数

力为能量负梯度：$$\mathbf{f}(\mathbf{x}) = -\nabla E(\mathbf{x}) = -k \left( ||\mathbf{x}|| - L \right) \left( \frac{\partial ||\mathbf{x}||}{\partial \mathbf{x}} \right)^\mathrm{T}$$利用 [向量范数导数推导](#向量范数导数推导) $\frac{\partial ||\mathbf{x}||}{\partial \mathbf{x}} = \frac{\mathbf{x}^\mathrm{T}}{||\mathbf{x}||}$，可得：$$\mathbf{f}(\mathbf{x}) = -k \left( ||\mathbf{x}|| - L \right) \frac{\mathbf{x}}{||\mathbf{x}||}$$

切向刚度（Tangent stiffness / Hessian）定义为力对位移的导数：$$\mathbf{H}(\mathbf{x}) = -\frac{\partial \mathbf{f}(\mathbf{x})}{\partial \mathbf{x}}$$展开后：$$\mathbf{H}(\mathbf{x}) = k \frac{\mathbf{x} \mathbf{x}^\mathrm{T}}{||\mathbf{x}||^2} + k \left( 1 - \frac{L}{||\mathbf{x}||} \right) \left( \mathbf{I} - \frac{\mathbf{x} \mathbf{x}^\mathrm{T}}{||\mathbf{x}||^2} \right)$$

### 两端自由弹簧系统

设两端位置为$\mathbf{x}_0$和$\mathbf{x}_1$，相对向量$\mathbf{x}_{01} = \mathbf{x}_0 - \mathbf{x}_1$，能量为：$$E(\mathbf{x}) = \frac{k}{2} \left( ||\mathbf{x}_{01}|| - L \right)^2$$
此处的 $\mathbf{x} = [\mathbf{x_0}, \mathbf{x_1}]^\top$ 
系统总力向量为两端受力的组合：$$\mathbf{f}(\mathbf{x}) = -\nabla E(\mathbf{x}) = \begin{bmatrix} -\nabla_0 E(\mathbf{x}) \\ -\nabla_1 E(\mathbf{x}) \end{bmatrix} = \begin{bmatrix} \mathbf{f}_e \\ -\mathbf{f}_e \end{bmatrix}$$其中单端力为：$$\mathbf{f}_e = -k \left( ||\mathbf{x}_{01}|| - L \right) \frac{\mathbf{x}_{01}}{||\mathbf{x}_{01}||}$$
Hessian矩阵为二阶导数矩阵：
$$\mathbf{H}(\mathbf{x}) = \begin{bmatrix} \frac{\partial^2 E}{\partial \mathbf{x}_0^2} & \frac{\partial^2 E}{\partial \mathbf{x}_0 \partial \mathbf{x}_1} \\ \frac{\partial^2 E}{\partial \mathbf{x}_0 \partial \mathbf{x}_1} & \frac{\partial^2 E}{\partial \mathbf{x}_1^2} \end{bmatrix} = \begin{bmatrix} \mathbf{H}_e & -\mathbf{H}_e \\ -\mathbf{H}_e & \mathbf{H}_e \end{bmatrix}$$其中子块$\mathbf{H}_e$为：$$\mathbf{H}_e = k \frac{\mathbf{x}_{01} \mathbf{x}_{01}^\mathrm{T}}{||\mathbf{x}_{01}||^2} + k \left( 1 - \frac{L}{||\mathbf{x}_{01}||} \right) \left( \mathbf{I} - \frac{\mathbf{x}_{01} \mathbf{x}_{01}^\mathrm{T}}{||\mathbf{x}_{01}||^2} \right)$$
### 向量范数导数推导

利用链式法则：$$\frac{\partial ||\mathbf{x}||}{\partial \mathbf{x}} = \frac{\partial (\mathbf{x}^\mathrm{T} \mathbf{x})^{1/2}}{\partial \mathbf{x}} = \frac{1}{2} (\mathbf{x}^\mathrm{T} \mathbf{x})^{-1/2} \cdot \frac{\partial (\mathbf{x}^\mathrm{T} \mathbf{x})}{\partial \mathbf{x}}$$其中，以三维向量$\mathbf{x} = [x, y, z]^\mathrm{T}$为例：$$\frac{\partial (\mathbf{x}^\mathrm{T} \mathbf{x})}{\partial \mathbf{x}} = \frac{\partial (x^2 + y^2 + z^2)}{\partial \mathbf{x}} = [2x \quad 2y \quad 2z] = 2 \mathbf{x}^\mathrm{T}$$代入得：$$\frac{\partial ||\mathbf{x}||}{\partial \mathbf{x}} = \frac{1}{2 ||\mathbf{x}||} \cdot 2 \mathbf{x}^\mathrm{T} = \frac{\mathbf{x}^\mathrm{T}}{||\mathbf{x}||}$$


---

# FIN