> 注：前面有一部分的内容是使用的L5的PPT

# 弹簧系统的缺陷

当两个平面弯折程度很小的时候，弹簧起到的抵抗作用会很弱
解决方法1：Dihedral Angle Model
解决方法2：Quadratic Bending Model

## Dihedral Angle Model（二面角模型）

主要思想：不使用长度而是通过夹角来计算抵抗
参考文献：[Bridson et al. 2003. Simulation of Clothing with Folds and Wrinkles. SCA.](GAMES103-基于物理的计算机动画入门/documents/GAMES103-main/After-class%20reading%20list/lec5_Bridson%20et%20al.2003.Simulation%20of%20Clothing%20with%20Folds%20and%20Wrinkles.SCA..pdf)

将 Bending Force 分别以方向和大小方式进行表示：$$\mathbf{f}_i = f(\theta) \mathbf{u}_i$$其中 $\theta$ 是两个平面之间夹角的大小。

力的大小分为两种情况进行，一种是最为基础的——当两个三角形在同一平面时抵抗为 $0$；一种是假设两个平面在初始状态下有原本的初始角度，当它们的角度为设定的角度的时候抵抗为 $0$ 
![](二面角模型（力的大小）.png)

力的方向则基于以下几点假设：
- $\mathbf{u}_1$ 和 $\mathbf{u}_2$ 需要在两个平面分别的法向量 $\mathbf{n}_1$ 和 $\mathbf{n}_2$ 上
- 弯折不会拉伸边本身 → $\mathbf{u}_3 - \mathbf{u}_4$ 需要垂直于 $\mathbf{x}_3 \mathbf{x}_4$ 这条弯折的边，也就是说需要在由 $\mathbf{n}_1$ 和 $\mathbf{n}_2$ 组成的平面上
- 因为所有的力本质上都是系统内部的力，因此合力为 $0$ → $\mathbf{u}_1 + \mathbf{u}_2 + \mathbf{u}_3 + \mathbf{u}_4 = 0$ 
最后能够得到最终的力的表示如下图所示：
![](GAMES103-基于物理的计算机动画入门/images/二面角模型（力的方向）.png)缺点：该方法是完全基于力进行计算的，没有能量方面的约束
优点：使用显式积分较容易实现

## Quadratic Bending Model（二次弯曲模型）

参考文献：[Bergou et al. 2006. A Quadratic Bending Model for Inextensible Surfaces. SCA.](GAMES103-基于物理的计算机动画入门/documents/GAMES103-main/After-class%20reading%20list/lec5_Bergou%20et%20al.2006.A%20Quadratic%20Bending%20Model%20for%20lnextensible%20Surfaces.SCA..pdf)

假设：
- 两边三角形静止的时候在同一平面上
- 拉伸量非常小
在上述假设的基础上能够有以下现成的公式：
![](二次弯曲模型公式.png)
因此我们非常容易就能够得到以下公式：
![](二次弯曲模型公式（f与H）.png)
优点：容易求得力与 Hessian 矩阵，便于隐式积分方法的实现
缺点：当布料拉伸程度过大的时候就会失效，无法适应两边三角形原始状态并非同一平面的情况

# Locking Issue

原先我们是将弯曲和拉伸的问题分别进行考虑的，然而弯曲会被拉伸的弹簧所影响，当网格划分不合理或者布料的物理属性设置在某些极端区间的时候（如拉伸的弹性系数被设置的很大）会导致物体的刚度（Stiffness）被过度高估，从而使得布料无法自由变形——被锁死。

这个问题的本质是自由度缺失的问题。对于原本的网格模型来说，每一个节点都拥有自己的自由度能够自由移动，然而使用了弹簧进行约束了之后，自由度的数量大大减少，因此产生了 Locking 的问题。

参考文献：[English and Bridson. 2008. Animating Developable Surfaces Using Nonconforming Elements. SIGGRAPH.](GAMES103-基于物理的计算机动画入门/documents/GAMES103-main/After-class%20reading%20list/lec5_English%20and%20Bridson.%202008.%20Animating%20Developable%20Surfaces%20Using%20Nonconforming%20Elements.%20SIGGRAPH..pdf)

# Stiffness Issue

物理模拟中的约束是用来解决真实世界与模拟之间的问题的，然而这其中的一大难点就是这些解决方法很多时候会带来巨大的、额外的计算量，因此我们希望找到一种方法能够在弹性系数很大的时候也能够比较简单地去进行模拟。

### Single Spring

对于只有单独一个弹簧的情况下，可以设置约束为其原长，并且严格限制最后的状态需要满足投影函数（Projection Function）$\phi(x)$ 为 $0$；由此可得以下的更新公式：
![](Stiffness%20issue%20-%20single%20spring.png)

### Multiple Spring

以下为涉及多根弹簧的时候可以使用的2种方法。

#### Gauss-Seidel Approach

核心思想：使用迭代和循环的方法进行近似值的计算——根据顺序不断更新各个弹簧的位置等信息，最后能够呈现出收敛的趋势
![](Gauss-Seidel%20Approach%20示意图.png)
![](Gauss-Seidel%20Approach%20流程图.png)
缺点：对边的顺序会产生依赖性

#### Jacobi Approach

相比于 Gauss-Seidel 方法，Jacobi 方法选择先不直接更新位置等信息，而是选择将更新量保存起来，在最后完成所有边的计算之后，通过对于某一顶点所有的更新量进行取平均值等操作之后再进行更新。
目的：
1. 消除对边的依赖性
2. 能够再 GPU 上并行运算
![](Jacobi%20Approach%20流程图.png)
缺点：收敛速度相比 Gauss-Seidel 方法更慢。

# Position Based Dynamic（PBD）

参考文献：[Muller. 2008. Hierarchical Position Based Dynamics. VRIPHYS.](GAMES103-基于物理的计算机动画入门/documents/GAMES103-main/After-class%20reading%20list/lec4&5_Muller%20et%20al.2005.Meshless%20Deformations%20Based%20on%20Shape%20Matching.TOG(SIGS%20GRAPH).pdf)

PBD 的思路和 Shape Matching 的思路十分类似，可以分为下面两部分：
1. 让所有点进行自由运动
2. 用投影函数进行约束
其特点就是并非根据物理进行实现，且收到迭代数量、网格的影响较大，这一点在弹性效果上会有所表现。

优点：容易并行，容易实现，通用性强
缺点：没有物理，没有精确解，在高分辨率的情况下收敛速度下降很快
应用：对于衣物要求不高的游戏

# Strain Limiting（应变限制）

核心思想：为物体的形变程度设定一个极限范围防止其被拉伸或压缩得太过分，从而让模拟效果更加符合真实物理规律。

用途：
- Strain Limiting 在模拟中不容易产生大形变，会更加地稳定
- Nonlinear Effects：对于某一些物体，它们在形变小的时候力很小，但是在形变达到某一长度的时候会感觉增长速度变快，而这一点 strain limiting 正好能够做到。

针对不同的情况，可以选择使用或者不适用 strain limiting（e.x. 史莱姆的情况就正好需要大形变，因此可以不适用 strain limiting，而肌肉则需要更多约束防止大形变，因此可以适用 strain limiting）

## Spring Strain Limiting

在弹簧的应用方面，可以将其看作是被放宽了约束条件的PBD。
![](spring%20strain%20limiting%20示意图.png)
可以看到约束条件从原先的必须要变回到原长变成了有一定范围变动空间。
![](spring%20strain%20limiting%20流程图.png)

## Triangle Area Limit

除了能够应用于弹簧系统，还可以应用于三角形的面积。
![](triangle%20area%20limit%20示意图.png)
注：在这里我们需要确保质心并没有发生移动。
![](triangle%20area%20limit%20流程图.png)

