> 注：后半部分是 L08 的PPT的内容。

# Projective Dynamics

参考文献：[Bouaziz et al. 2014. Projective Dynamics: Fusing Constraint Projections for Fast Simulation. TOG (SIGGRAPH).](GAMES103-基于物理的计算机动画入门/documents/GAMES103-main/After-class%20reading%20list/lec6_Bouaziz%20et%20al.2014.Projective%20Dynamics%20-%20Fusing%20Constraint%20Projections%20for%20Fast%20Simulation.TOG(SIGGRAPH).pdf)

与 PBD 中采用 Jacobi 或 Gauss-Seidel 方式混合投影不同，projective dynamics 使用投影来定义一个二次能量。PBD 的方式是一个点一个点去进行调整，而 projective dynamics 更偏向通过给整体计算一个最优答案进行调整。
![](Projective%20Dynamics%20定义公式.png)
公式里的能量 $E(x)$ 就是最优答案的衡量标准，它衡量的是当前状态和理想状态之间的差距。
因为物理世界里，物体变形时会“抗拒”变化，而使用这个能量公式，就是在数学上模拟这种“抗拒”；能量越小，说明物体越接近它“想成为”的样子。

通过对能量进行求导能够得到力于 Hessian 矩阵。由下图的过程可以看到最后求得的 **Hessian 矩阵是一个常数，同时也是该方法的核心**。
![](Projective%20Dynamics%20公式过程.png)
由此，便只需要进行 1 次 LU 分解，这大大减小了计算量。
![](Projective%20Dynamics%20流程图.png)

优点：相比 PBD 来说，这种方法具有物理含义
缺点：尽管在 CPU 上能够运行得很快，但是不适用于 GPU

# Constrained Dynamics

这种方法适用于约束或力比较严格的情况，比如说刚体模拟，或者是人体等限制较多的情况。

使用约束违反量 $\phi_e$ 来计算惩罚能量 $\mathbf{E}(x)$，其中 $k$ 是劲度系数，$k$ 越大惩罚越重。
![](Constrained%20Dynamics%20公式1.png)
同时使用 $\mathbf{\lambda}$ 来作为第二个变量。

根据上述的公式做隐式积分，能够得到：
![](GAMES103-基于物理的计算机动画入门/images/Constrained%20Dynamics%20公式2.png)
由于和之前不同，此种方法由两个变量，因此需要同时解出来：
- Method 1：Primal-dual 方法——直接求解
- Method 2：Schur Complement 方法——先求解 $\mathbf{\lambda}^{\text{new}}$，再代入 $\mathbf{v}^{\text{new}}$ 

对于该方法中的 Hessian 矩阵，
![](GAMES103-基于物理的计算机动画入门/images/Stable%20Constrained%20Dynamics.png)

# 有限元

## FEM（Linear Finit Element Method）

假设：对于三角形中的任意一点的变化均能够用 $\mathbf{x} = \mathbf{F} \mathbf{X} + \mathbf{c}$ 这个线性公式来描述。
![](GAMES103-基于物理的计算机动画入门/images/Linear%20Finit%20Element%20Method.png)

求解 $\mathbf{F}$：![](GAMES103-基于物理的计算机动画入门/images/FEM%20求解%20F%201.png)
![](GAMES103-基于物理的计算机动画入门/images/FEM%20求解%20F%202.png)
求解出 $\mathbf{F}$ 之后就自然能够求解出 $\mathbf{c}$ 了。

其中 $\mathbf{F}$ 表示了物体从原始状态到变形状态的完整映射，包含变形和旋转。根据 SVD，能够有 $\mathbf{F} = \mathbf{U} \mathbf{D} \mathbf{V}^\top$。因此，为了得到一个变量满足“只描述形变，不包含旋转"”这个目标，构造出了 Green Strain（格林应变）：$$\mathbf{G} = \frac{1}{2}(\mathbf{F}^\top \mathbf{F} - \mathbf{I}) = \frac{1}{2} (\mathbf{V} \mathbf{D}^2 \mathbf{V}^\top - \mathbf{I}) = \begin{bmatrix} \varepsilon_{uu} & \varepsilon_{uv} \\ \varepsilon_{uv} & \varepsilon_{vv} \end{bmatrix}$$
其中 $\mathbf{I}$ 是为了能够在 $\mathbf{F} = \mathbf{I}$ 的时候让 $\mathbf{G} = 0$。
- 如果没有变形， $\mathbf{G} = 0$；如果变形增加， $∥\mathbf{G}∥$ 增加。
- 三种变形模式：$\varepsilon_{uu}$、$\varepsilon_{uv}$ 和 $\varepsilon_{vv}$。

### Strain Energy Density Function

如果将能量密度表示为 $W(\mathbf{G})$，那么有：
![](GAMES103-基于物理的计算机动画入门/images/Strain%20Energy%20Density%20Function.png)
由此可以得到力的表达方式：![](GAMES103-基于物理的计算机动画入门/images/Strain%20Energy%20Density%20Function%20--%20Force.png)
为了得到更加简洁的表示方式，可以见下方的推导过程：
![](GAMES103-基于物理的计算机动画入门/images/Strain%20Energy%20Density%20Funciton%20--%20Force%20推导过程1.png)
![](GAMES103-基于物理的计算机动画入门/images/Strain%20Energy%20Density%20Funciton%20--%20Force%20推导过程2.png)
![](GAMES103-基于物理的计算机动画入门/images/Strain%20Energy%20Density%20Funciton%20--%20Force%20推导过程3.png)
最后力可以直接由上述简单的公式表示。

