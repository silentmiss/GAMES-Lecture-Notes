```
抽象化：Structured Spring Networks & Unstructured Spring Networks 
                                    ↓
bendings → Topological Contruction ← Triangle Mesh Representation

计算方式：
Explicit Integration → 问题
Implicit Integration：Newton Method & Jacobi Method
```

对于较为规则的情况，将布料看作是方形网格，节点之间通过弹簧连接从而进行约束。
约束方式除了横向与纵向，还有对角线上的限制以及跨越节点的限制。
将网格之间的约束进行简化，可以减少一些对角线以及节点之间的约束，但是需要注意对角线上的约束不能够只有一个方向的以免出现偏向性。
![](GAMES103-基于物理的计算机动画入门/images/Structured%20Spring%20Networks.png)

对于不太规则的情况，可以将不了看作是三角形，同样通过弹簧来连接节点从而起到约束的作用。
弹簧除了连接三角形本来的边，还可以连接原先没有连接的节点。
![](GAMES103-基于物理的计算机动画入门/images/Unstructured%20Spring%20Networks.png)
但是如何判断哪些节点之间需要新的连接相较于方形网格的情况更为复杂。


# 补充知识

## 弹簧系统

![](GAMES103-基于物理的计算机动画入门/images/Ideal%20Spring.png)

![](GAMES103-基于物理的计算机动画入门/images/Multiple%20Springs.png)

## Triangle Mesh Representation

![](GAMES103-基于物理的计算机动画入门/images/Triangle%20Mesh%20Representation.png)
