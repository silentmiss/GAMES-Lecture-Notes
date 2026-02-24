```markdown
2种实现方式：
	- 基于优化的方法
		2种碰撞检测+响应方式（针对某一个点）：
			- quadratic penalty mehtod
			- impulse method
	- 基于图形学的方法——shape matching
```

# 基于优化的方法

虽然 Impulse Method 能够更加精确地控制反弹和摩擦，但是真实世界的机制其实更接近 Penalty Method 
## Quadratic Penalty Method

当一个点在平面内部 $\phi(x) < 0$ 的时候，需要提供一个让其从平面出来的 penalty force $$f \leftarrow -k \phi(x) N$$
问题1：会有穿透的瑕疵
解决方法：提前设置一个距离 $$\phi(x) < \epsilon \longrightarrow f \leftarrow k (\epsilon - \phi(x)) N$$

问题2：$k$ 的设置比较困难，如果太大的就会不合理导致 overshooting 的问题
解决方法：不将 $k$ 设置成常数 $$f \leftarrow \rho \frac{1}{\phi(x)} N$$
- $\rho$：barrier strength

问题3：如果点距离平面太近也可能导致 overshooting 的问题
解决方法：小步长

问题4：需要保证 $\phi(x) < 0$ 不会发生，不然 $f$ 方向反而会导致物体越陷越深

## Impulse Method

![[images/impulse method.png]]

与 penalty method 的区别在于：penalty method 在检测到碰撞之后会产生一个力但不会立刻执行，需要等到下一个时刻；impulse method 则会立刻执行

位置更新：
$$\phi(x) < 0 \longrightarrow \text{collision: } x^{\text{new}} \leftarrow x + |\phi(x)| N = x - \phi(x) \nabla \phi(x)$$

速度更新：
$$
\begin{align}
& v \cdot N < 0 \\
\longrightarrow & \begin{cases} v_\text{N} \leftarrow (v \cdot N) N \\ v_\text{T} \leftarrow v - v_N \end{cases} \\
\longrightarrow & \begin{cases} v_\text{N}^\text{new} \leftarrow -\mu_\text{N} v_\text{N} \\ v_\text{T}^\text{new} \leftarrow a v_\text{T} \end{cases} \\
\longrightarrow & v^\text{new} \leftarrow v_\text{N}^\text{new} + v_\text{T}^\text{new}
\end{align}
$$
- $\mu_\text{N}$：反弹系数，为了符合能量守恒定律，取值范围为 $[0, 1]$ 
- $a$：需要保证尽量小的同时满足库仑定律（Coulomb's law）
  $$\begin{align} & || v_\text{T}^\text{new} - v_\text{T} || \leq \mu_\text{N} || v_\text{N}^\text{new} - v_\text{N} || \\ \Rightarrow & a \leftarrow \max(\frac{1 - \mu_\text{T}(1 + \mu_\text{N}) || v_\text{N} ||}{|| v_\text{T} ||}, 0) = \max(\text{dynamic friction}, \text{static friction})\end{align}$$

但是实际情况中仅设置了 4 个变量（质心的位置、质心的速度、旋转轴的姿势、旋转的角速度），因此无法直接更新 $x_i$ 和 $v_i$，所以可以通过修改 $\omega$ 和 $v$ 这一较为容易的方法进行更新
$$
\begin{cases}
x_i \leftarrow x + R r_i \\
v_i \leftarrow v + \omega \times R r_i
\end{cases}
$$
由于 $\frac{j}{m} = \Delta v$，因此 $$v^\text{new} \leftarrow v + \frac{1}{M} j$$
由于 $v_i^\text{new} = v^\text{new} + \omega_\text{new} \times R r_i$，因此
$$
\begin{align}
v_i^\text{new} & = v + \frac{1}{M} j + (\omega + I^{-1}(R r_i \times j)) \times R r_i \\
& = v_i + \frac{1}{M} j + (I^{-1} (R r_i \times j)) \times R r_i \\
& = v_i + \frac{1}{M} j - R r_i \times (I^{-1} (R r_i \times j)) \\
& = v_i + \frac{1}{M} j - (R r_i)^{*} I^{-1} (R r_i)^{*} j \text{ （四元数的差积计算）}
\end{align}
$$
提炼出系数之后能够简化为以下的流程 
$$
\begin{cases}
K \leftarrow \frac{1}{M} \mathbb{1} - (R r_i)^{*} I^{-1} (R r_i)^{*} \\
v_i^\text{new} - v_i = K j
\end{cases}
$$

以上只是对于单点进行处理，如果出现多个点发生了碰撞的情况，可以选择取各个点的位置平均值及逆行计算来简化问题（对于多个点分别计算也可以，但是可能需要考虑的因素会更多）

> 补充：
> 问题：由于对于物体来说永远有重力的作用，因此可能会因为一直产生重力方向的冲量从而发生抖动（Oscillation）
> 解决方法：在物体开始精致下来的时候可以尝试减小 $\mu_\text{N}$ 来减小抖动

---

# 基于图形学的方法——Shape Matching

基本思想：类似例子系统，①先让所有点都能够自由运动，②之后再通过约束让其回到原先的结构

好处：比较容易实现
缺点：无法保证所有的约束都符合，需要通过迭代取进行反复约束
运用场景：当进行布料模拟的时候，衣物会与刚体发生碰撞，可以考虑 Shape Matching

### ① 点的运动

![[images/Shape Matching.png]]

### ② 点的约束

![[images/点的约束.png]]
目的：$$y_i \rightarrow x_i = c + R r_i$$
将问题转换成求解未知数 $c$ 和 $R$：$$\{c, R\} = \text{argmin} \sum_i \frac{1}{2} || c + R r_i - y_i ||^2$$ 因为这里 $R$ 实际上可以通过扩大问题替换成任意矩阵，因此可以简化成下面的形式：$$\{c, A\} = \text{argmin} \sum_i \frac{1}{2} || c + A r_i - y_i ||^2 = \text{argmin} \sum_i E \text{（目标 Objective）}$$
通过计算偏微分能够得到下面的算式：
$$
\begin{align}
& \frac{\partial E}{\partial c} = \sum_i c+ A r_i - y_i = \sum_i c - y_i = 0 \\
\Rightarrow & c = \frac{1}{N} \sum_i y_i
\end{align}
$$
$$
\begin{align}
& \frac{\partial E}{\partial A} = \sum_i (c + A r_i - y_i) r_i^\top = 0 \\
\Rightarrow & A = (\sum_i (y_i - c) r_i^\top) (\sum_i r_i r_i^\top)^{-1} = RS \text{（Polar Decomposition）}
\end{align}
$$


---
# 补充知识

## 力矩

力矩：造成物体旋转的一种趋势

## 判断一个点与平面的关系

signed distance function 
$$
\phi(x) = 
\begin{cases}
> 0 & \text{outside} \\
= 0 & \text{on the surface} \\
< 0 & \text{inside}
\end{cases}
$$
### 情况1：点是否在由多个平面包围的物体内部

$$\phi(x) = \max(\phi_1(x), \phi_2(x), \dots) = \begin{cases} < 0 & \text{inside} \\ \geq 0 & \text{other} \end{cases}$$

### 情况2：点是否同时在两个物体内部/外部

判断是否在两个物体内部：
$$\phi(x) \approx \min(\phi_1(x), \phi_2(x)) = \begin{cases} < 0 & \text{inside} \\ \geq 0 & \text{other} \end{cases}$$
判断是否在两个物体外部：
$$\phi(x) = \min(\phi_1(x), \phi_2(x)) = \begin{cases} > 0 & \text{outside} \\ \leq 0 & \text{other} \end{cases}$$

## 四元数的叉积计算

通常，两个向量 $\mathbf{a}$ 和 $\mathbf{b}$ 的叉积是：
$$
\mathbf{a} \times \mathbf{b} = (a_yb_z - a_zb_y,\; a_zb_x - a_xb_z,\; a_xb_y - a_yb_x)
$$
但我们可以把它改写成**矩阵乘以向量**的形式：
$$
\mathbf{a} \times \mathbf{b} = [\mathbf{a}]_{\times} \mathbf{b}
$$
其中 $[\mathbf{a}]_{\times}$ 就是代码中构造的**斜对称矩阵**（Skew-Symmetric Matrix）。

对于向量 $\mathbf{a} = (a_x, a_y, a_z)$，其**叉积矩阵**为：

$$
[\mathbf{a}]_{\times} = \begin{bmatrix} 
0 & -a_z & a_y \\ 
a_z & 0 & -a_x \\ 
-a_y & a_x & 0 
\end{bmatrix}
$$
验证：

$$
\begin{bmatrix} 
0 & -a_z & a_y \\ 
a_z & 0 & -a_x \\ 
-a_y & a_x & 0 
\end{bmatrix} 
\begin{bmatrix} b_x \\ b_y \\ b_z \end{bmatrix} 
= 
\begin{bmatrix} 
-a_z b_y + a_y b_z \\ 
a_z b_x - a_x b_z \\ 
-a_y b_x + a_x b_y 
\end{bmatrix} 
= 
\mathbf{a} \times \mathbf{b}
$$

# FIN