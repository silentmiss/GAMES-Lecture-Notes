```
抽象化：Structured Spring Networks & Unstructured Spring Networks 
                                    ↓
bendings → Topological Contruction ← Triangle Mesh Representation

计算方式：
Explicit Integration → 问题
Implicit Integration：Newton Method & Jacobi Method
```

# 建模

## Structured Spring Networks

对于较为规则的情况，将布料看作是方形网格，节点之间通过弹簧连接从而进行约束。
约束方式除了横向与纵向，还有对角线上的限制以及跨越节点的限制。
将网格之间的约束进行简化，可以减少一些对角线以及节点之间的约束，但是需要注意对角线上的约束不能够只有一个方向的以免出现偏向性。
![](GAMES103-基于物理的计算机动画入门/images/Structured%20Spring%20Networks.png)

## Unstructured Spring Networks

对于不太规则的情况，可以将不了看作是三角形，同样通过弹簧来连接节点从而起到约束的作用。
弹簧除了连接三角形本来的边，还可以连接原先没有连接的节点。
![](GAMES103-基于物理的计算机动画入门/images/Unstructured%20Spring%20Networks.png)
但是如何判断哪些节点之间需要新的连接相较于方形网格的情况更为复杂。

在 [Triangle Mesh Representation](#Triangle%20Mesh%20Representation) 的基础上，构造新的 triple 列表用于存储一条边的两个顶点和相对应的三角形的编号，然后对于新列表进行排序，如此得到的新列表中重复的边就会排序在一起，由此便可以轻易删除重复的节点然后得到已有的边的信息，于是就能够得到能够添加的新 bending 的列表。
![](GAMES103-基于物理的计算机动画入门/images/Topological%20Contruction.png)

---
# 计算

## Explicit Integration

计算流程：首先得到弹簧的两个节点，通过当前时刻的弹簧长度计算分别对于两个节点的力，然后通过粒子系统的方式进行速度与位置的更新
![](GAMES103-基于物理的计算机动画入门/images/Explicit%20Integration.png)

问题：该种计算方式容易产生数值不稳定（Numerical Instability）的问题——当 $k$ 或 $\Delta t$ 过大的时候，会出现 overshooting 的现象。

## Implicit Integration

![](GAMES103-基于物理的计算机动画入门/images/Implicit%20Integration.png)
对于 $\mathbf{f}^{[1]}$，我们假设其只与位置有关，不依赖于其他变量（holonomic）。
注：其中 $\mathbf{M} \in \mathbb{R}^{3N \times 3N}$ 。

因此我们能够将上述 $\mathbf{x}^{[1]}$ 的计算转换成等价的求某函数最小值的系数的问题：
![](GAMES103-基于物理的计算机动画入门/images/问题转化.png)

### Newton-Raphson Method

![](GAMES103-基于物理的计算机动画入门/images/Newton-Raphson%20Method.png)
此处一个重要的计算就是
$$\frac{\partial F^2 (\mathbf{x}^{(k)})}{\partial \mathbf{x}^2} = \frac{1}{\Delta t^2} \mathbf{M} + \mathbf{H}(\mathbf{x}^{(k)})$$
其中牵涉到前面课程提及的 Hessian 知识点。
$$
\mathbf{H}(\mathbf{x}) 
= \sum_{e={i,j}} 
\begin{bmatrix} \square & \square & \square & \square \\ \square & \frac{\partial^2 E}{\partial \mathbf{x}_i^2} & \frac{\partial^2 E}{\partial \mathbf{x}_i \partial \mathbf{x}_j} & \square \\ \square & \frac{\partial^2 E}{\partial \mathbf{x}_i \partial \mathbf{x}_j} & \frac{\partial^2 E}{\partial \mathbf{x}_j^2} & \square \\ \square & \square & \square & \square \end{bmatrix} 
= \sum_{e={i,j}} 
\begin{bmatrix} \square & \square & \square & \square \\ \square & \mathbf{H}_e & -\mathbf{H}_e & \square \\ \square & -\mathbf{H}_e & \mathbf{H}_e & \square \\ \square & \square & \square & \square \end{bmatrix}
$$
$$
\mathbf{H}_e 
= k \frac{\mathbf{x}_{ij}\mathbf{x}_{ij}^{\text{T}}}{||\mathbf{x}_{ii}||^2} 
+ k\left(1 - \frac{L}{||\mathbf{x}_{ij}||}\right)
\left(\mathbf{I} - \frac{\mathbf{x}_{ij}\mathbf{x}_{ij}^{\text{T}}}{||\mathbf{x}_{ii}||^2}\right)
$$
- $k \frac{\mathbf{x}_{ij}\mathbf{x}_{ij}^{\text{T}}}{||\mathbf{x}_{ii}||^2}$：半正定（s.p.d.）
- $\left(1 - \frac{L}{||\mathbf{x}_{ij}||}\right)$：若 $|\mathbf{x}_{ij}| < L_e$ 则为负
- $\left(\mathbf{I} - \frac{\mathbf{x}_{ij}\mathbf{x}_{ij}^{\text{T}}}{||\mathbf{x}_{ii}||^2}\right)$：半正定（s.p.d.）
这是因为对于任意 $\mathbf{x}_{ij}, \mathbf{v} \neq \mathbf{0}$，
$$ \mathbf{v}^{\text{T}} \frac{\mathbf{x}_{ij}\mathbf{x}_{ij}^{\text{T}}}{||\mathbf{x}_{ij}||^2} \mathbf{v} = \left|\left| \frac{\mathbf{x}_{ij}^{\text{T}} \mathbf{v}}{||\mathbf{x}_{ij}||} \right|\right|^2 > 0 $$
$$ \mathbf{v}^{\text{T}} \left( \mathbf{I} - \frac{\mathbf{x}_{ij}\mathbf{x}_{ij}^{\text{T}}}{||\mathbf{x}_{ij}||^2} \right) \mathbf{v} = \frac{||\mathbf{x}_{ij}||^2 ||\mathbf{v}||^2 - ||\mathbf{x}_{ij}^{\text{T}} \mathbf{v}||^2}{||\mathbf{x}_{ij}||^2} > 0 $$
因此可以注意到，当弹簧拉伸的时候，Hessian 矩阵是正定的，但是当弹簧收缩的时候 Hessian 矩阵的正定性就无法确定了——这一点可以体现在现实中我们想要压缩弹簧的时候，弹簧有可能会向两端弯折，而这种弯折的方向是不确定的，正好对应了 Hessian 矩阵无法确定的正定性。
![](GAMES103-基于物理的计算机动画入门/images/Positive%20Definiteness%20of%20Hessian.png)
对于上述 Hessian 矩阵的正定性的问题，有一种解决方法是在 Hessian 矩阵不是正定的时候直接去掉其后半部分从而使其保持正定。

### Jacobi Method

![](GAMES103-基于物理的计算机动画入门/images/Jacobi%20Method.png)
$\alpha$的重要性：标准雅可比方法（α = 1）要求矩阵 A 必须“对角占优”（即对角线元素远大于其他元素），否则可能不收敛；引入 $α$ 后，即使 A 只是“正定矩阵”（一种更宽松的条件），只要选择合适的 $α$，算法也能收敛，这大大扩展了雅可比方法的适用范围。

#### 使用 Chebyshev 进行加速

![](GAMES103-基于物理的计算机动画入门/images/The%20Jacobi%20Method%20with%20Chebyshev%20Acceleration.png)

### 对比两种方法

Direct Method：对于矩阵的要求少，大多数情况都能够使用，求解精确；计算逻辑复杂很难在GPU上并行化加速，而且计算量大
Jacobi Method：要求矩阵性质好（正定），求解不够精确；计算快捷且代码简单清晰

---

# 补充知识

## 弹簧系统

![](GAMES103-基于物理的计算机动画入门/images/Ideal%20Spring.png)

![](GAMES103-基于物理的计算机动画入门/images/Multiple%20Springs.png)

## Triangle Mesh Representation

![](GAMES103-基于物理的计算机动画入门/images/Triangle%20Mesh%20Representation.png)
在电脑中，三角形通过上面图片的方式进行存储，一个列表用于存储节点的位置，一个聊表用于存储三角形三条边的索引。

