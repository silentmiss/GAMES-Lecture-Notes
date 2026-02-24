```
内容大纲：
刚体模拟（平移 & 旋转）

注：为了方面没有特地使用加粗符号表示向量
```

刚体（Rigid Body）模拟 → 随时间通过模拟器更新状态量 $s^{[i]}$

![[Rigid Body Simulation.png]]

---

# Translational Motion（平移）

在刚体的情况下，状态量由两个量联合表示：
- 位置 $x(t^{[i]})$
- 速度 $v(t^{[i]})$

更新方式：
$$x^(t^{[1]}) = x(t^{[0]}) + \int_{t^{[0]}}^{t^{[1]}} v(t) dt$$
$$v(t^{[1]}) = v(t^{[0]}) + M^{-1} \int_{t^{[0]}}^{t^{[1]}} f(x(t), v(t), t) dt$$
- 质量 $M$ 
- 受力 $f(x(t), v(t), t)$

## Integration Methods Explained

### Euler Explicit (1st-order accurate)
$$\int_{t^{[0]}}^{t^{[1]}} v(t) dt \approx \Delta t v(t^{[0]})$$
解析：
$$\int_{t^{[0]}}^{t^{[1]}} v(t) dt = \Delta t v(t^{[0]}) + \frac{\Delta t^2}{2} v'(t^{[0]}) = \Delta t v(t^{[0]}) + \mathcal{O}(\Delta t^2)$$


### Euler Implicit (1st-order accurate)
$$\int_{t^{[0]}}^{t^{[1]}} v(t) dt \approx \Delta t v(t^{[1]})$$
解析：
$$\int_{t^{[0]}}^{t^{[1]}} v(t) dt = \Delta t v(t^{[1]}) - \frac{\Delta t^2}{2} v'(t^{[1]}) = \Delta t v(t^{[1]}) + \mathcal{O}(\Delta t^2)$$

### Mid-point (2nd-order accurate)
$$\int_{t^{[0]}}^{t^{[1]}} v(t) dt \approx \Delta t v(t^{[0.5]})$$
解析：
$$
\begin{align}
\int_{t^{[0]}}^{t^{[1]}} v(t) dt &= \int_{t^{[0]}}^{t^{[0.5]}} v(t) dt + \int_{t^{[0.5]}}^{t^{[1]}} v(t) dt \\
&= \frac{1}{2} \Delta t v(t^{[0.5]}) - \frac{\Delta t^2}{2} v'(t^{[0.5]}) +  \frac{1}{2} \Delta t v(t^{[0.5]}) + \frac{\Delta t^2}{2} v'(t^{[0.5]}) \\ 
&= \Delta t v(t^{[0.5]})
\end{align}
$$

## Leapfrog Integration

$$
\begin{cases}
v^{[0.5]} = v^{[-0.5]} + \Delta t M^{-1} f^{[0]} & \\
x^{[1]} = x^{[0]} + \Delta t v^{[0.5]} & \text{(semi-implicit)}
\end{cases}
$$

---

# Rotational Motion（旋转）

不同表示方法：
- 旋转矩阵（Rotation Matrix）
	- 问题：具有很大的[[#冗余性]]，不太直观
- 欧拉角（Euler Angles）
	- 问题：[[#万向锁（Gimbal Lock）]] 
- 四元数（Quaternion）
	- 优点：冗余小，不存在万向锁的问题

力矩（Torque）：$$\tau_i = R r_i \times f_i$$
总力矩：$$\tau = \sum \tau_i$$
转动惯量（Moment of Inertia）
- 【参考状态——物体坐标系】：
$$
I_{\text{ref}} = \sum m_i (r_i^{\top} r_i \mathbf{1} - r_i r_i^{\top}) 
= \sum_i m_i \begin{bmatrix} 
y_i^2+z_i^2 & -x_iy_i & -x_iz_i \\ 
-x_iy_i & x_i^2+z_i^2 & -y_iz_i \\ 
-x_iz_i & -y_iz_i & x_i^2+y_i^2 
\end{bmatrix}
$$
- 【世界坐标系】：$$I = R I_{\text{ref}} R^{\top}$$
参数说明：
- $r_i = (x_i, y_i, z_i)$：力作用点相对于质心的位置向量
- $f_i$：外力
- $R$：旋转矩阵

四元数 $q = (w, x, y, z)$ 与旋转矩阵的转换：![[四元数与旋转矩阵的转换.png]]


## 冗余性

冗余：参数维度 > 空间真实维度
旋转矩阵用 9 个数存储，但其中 6 个是“被约束锁死”的，真正独立的信息只有 3 个。

### 具体解释过程

旋转矩阵一共有9个数（3×3 矩阵）：
$$
R =  
\begin{bmatrix}  
r_{11} & r_{12} & r_{13}\\  
r_{21} & r_{22} & r_{23}\\  
r_{31} & r_{32} & r_{33}  
\end{bmatrix}  
$$

但三维旋转实际只有3个自由度（不论是什么表示方式）

但是旋转矩阵不是任意 3×3 矩阵，它必须满足两个条件：
- 条件 1：正交 $$R^T R = I$$这意味着：
	- 每一列向量长度 = 1
	- 每两列互相垂直
- 条件 2：行列式 = 1 $$\det(R) = 1$$保证不是镜像翻转。

约束条件：
（1）每列单位长度
$$
\begin{align}
r_{11}^2 + r_{21}^2 + r_{31}^2 = 1\\
r_{12}^2 + r_{22}^2 + r_{32}^2 = 1\\
r_{13}^2 + r_{23}^2 + r_{33}^2 = 1
\end{align}
$$
（2）列之间正交
$$
\begin{align}
r_{11}r_{12}+r_{21}r_{22}+r_{31}r_{32}=0\\
r_{11}r_{13}+r_{21}r_{23}+r_{31}r_{33}=0\\
r_{12}r_{13}+r_{22}r_{23}+r_{32}r_{33}=0
\end{align}
$$
（3）行列式 = 1
共有 $7$ 个约束，但其中一个约束不是独立的（数学上可证明），因此最终独立约束是 $6$ 个
所以自由度是 $9 - 6 = 3$ 个，这就是三维旋转的本质维度。

## 万向锁（Gimbal Lock）

万向锁（Gimbal Lock）是指多轴装置在特定角度下丢失一个自由度的现象。具体来说，当三轴万向节中的两个轴转到平行时，系统就被"卡"住了，只能在二维平面内旋转，而无法自由转动。

万向锁不是物理上不能转，而是欧拉角在某些姿态下发生数学退化：两个旋转轴重合，导致角度表示不再唯一，控制变量之间失去独立性。结果是在该姿态附近数值不稳定、插值跳变、控制矩阵奇异。

> 参考：
> [动态与静态欧拉角视角下的万向节死锁（Gimbal Lock）问题 - 知乎](https://zhuanlan.zhihu.com/p/474447990)
> [Gimbal 锁定 - 维基百科 --- Gimbal lock - Wikipedia](https://en.wikipedia.org/wiki/Gimbal_lock)

#### 补充知识：三个基本旋转矩阵

- 绕 Z 轴（yaw）
$$
R_z(\psi) =  
\begin{bmatrix}  
\cos\psi & -\sin\psi & 0 \\  
\sin\psi & \cos\psi & 0 \\  
0 & 0 & 1  
\end{bmatrix}  
$$
- 绕 Y 轴（pitch）
$$
R_y(\theta) =  
\begin{bmatrix}  
\cos\theta & 0 & \sin\theta \\  
0 & 1 & 0 \\  
-\sin\theta & 0 & \cos\theta  
\end{bmatrix}  
$$
- 绕 X 轴（roll）
$$
R_x(\phi) =  
\begin{bmatrix}  
1 & 0 & 0 \\  
0 & \cos\phi & -\sin\phi \\  
0 & \sin\phi & \cos\phi  
\end{bmatrix}  
$$

### 静态欧拉角

静态欧拉角是在惯性坐标系下表示旋转的方式，惯性坐标系是固定不变的。

> **静态欧拉角 ZYX（外旋）** 的数学表达是：$$R = R_x(\text{roll}) ; R_y(\text{pitch}) ; R_z(\text{yaw})  $$

假设条件： pitch = 90°
也就是：$$\theta = 90^\circ$$
那么：$$\cos\theta = 0, \sin\theta = 1$$
代入：
$$
R_y(90^\circ) =  
\begin{bmatrix}  
0 & 0 & 1 \\  
0 & 1 & 0 \\  
-1 & 0 & 0  
\end{bmatrix}  
$$
计算：$$R = R_x(\phi) R_y(90^\circ) R_z(\psi)$$完整推导后矩阵会变成：
$$
R =  
\begin{bmatrix}  
0 & 0 & 1 \\  
\sin(\phi - \psi) & \cos(\phi - \psi) & 0 \\  
-\cos(\phi - \psi) & \sin(\phi - \psi) & 0  
\end{bmatrix}  
$$
它只依赖于：$$\phi - \psi$$不是分别依赖 φ 和 ψ。
这意味着原本 φ（roll）和 ψ（yaw）是两个独立变量，但现在矩阵只看：$$(\phi - \psi)$$
现在比较两种情况：
- 情况 A：$$\psi = 10^\circ, \quad \phi = 0$$
- 情况 B：$$\psi = 0, \quad \phi = 10^\circ$$
也就是说：$(\phi=10, \psi=0)$ 和 $(\phi=0, \psi=-10)$ 产生**完全相同的旋转矩阵** 
而这就是 **轴重合** 的真正含义——两个变量在数学上变成同一个变量，即发生了自由度坍缩。

### 动态欧拉角

动态欧拉角是在自身坐标系下表示旋转的方式，自身坐标系是会随着自身的旋转而改变的，需要实现定义好旋转轴的顺序（嵌套）。

> **动态欧拉角ZYX（内旋）** 的数学表达是：$$R = R_z(\psi), R_y(\theta), R_x(\phi)$$
> ==注意顺序和刚才不同==

假设$$\theta = 90^\circ$$即：
$$\cos\theta = 0, \sin\theta = 1$$
把它们相乘：$$R = R_z(\psi), R_y(90^\circ), R_x(\phi)$$完整代数推导后可以得到：
$$
R =  
\begin{bmatrix}  
0 & \sin(\phi+\psi) & \cos(\phi+\psi) \\  
0 & \cos(\phi+\psi) & -\sin(\phi+\psi) \\  
-1 & 0 & 0  
\end{bmatrix}  
$$
注意看矩阵里只出现 $\phi + \psi$，而不是 φ 和 ψ 分别出现。
这意味着当 pitch = 90° 时，旋转只依赖于 $\phi + \psi$
例如：
- 情况 A：$\phi=10^\circ,\quad \psi=0$ 
- 情况 B：$\phi=0,\quad \psi=10^\circ$ 
会产生**完全相同的旋转矩阵**。

---

## 四元数（Quaternion）

一个四元数写成：$$q = w + xi + yj + zk$$
也可以写成向量形式：$$q = (w, x, y, z)$$
单位四元数满足：$$w^2 + x^2 + y^2 + z^2 = 1$$
这保证它表示一个旋转。

### 旋转的核心思想

四元数表示旋转的本质是：用一个“轴 + 角度”编码旋转

如果旋转轴是单位向量：$$\mathbf{u} = (u_x, u_y, u_z)$$
旋转角度是：$$\theta$$
那么对应四元数是：
$$
q =  
\left(  
\cos\frac{\theta}{2},
u_x \sin\frac{\theta}{2},  
u_y \sin\frac{\theta}{2}, 
u_z \sin\frac{\theta}{2}  
\right)
$$

### 四元数旋转一个向量的方式

假设我们要旋转向量：$$\mathbf{v} = (v_x, v_y, v_z)$$
先把它变成“纯四元数”：$$v = (0, v_x, v_y, v_z)$$
然后计算：$$v' = q v q^{-1}$$
### 四元数的计算

![[四元数的计算.png]]
