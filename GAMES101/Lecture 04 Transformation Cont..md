How to take a photo:
- model transformation 
- view transformation
- projection transformation

# Viewing transformation

## View / Camera transformation （模型）视图变换

相机相关参数定义：
- Position $\vec{e}$ 
- Look-at / gaze direction $\hat{g}$ 
- Up direction $\hat{t}$ 

由于当场景移动的时候，也可以相当于相机相对于场景进行移动，因此定义相机永远在：
- the origin
- up at Y
- look at -Z

![[view transformation（矩阵推导）.jpg]]

## Projection transformation

- Orthographic projection 正交投影
	- drop Z coordinate
	- translate and scale the resulting rectangle to $[-1, 1]^2$ 
![[orthographic projection 矩阵推导.jpg]]
- Perspective projection 透视投影
![[perspective projection 矩阵推导.jpg]]
$$M_{\text{persp}} = M_{\text{persp→ortho}}M_{\text{ortho}}$$
![[齐次坐标 透视投影变换 乘数z的解释.png]]


# FIN