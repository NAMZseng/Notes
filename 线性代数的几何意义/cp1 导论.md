# cp1 导论

## 1 线性代数的含义

线性代数是研究线性空间里的向量或子空间之间线性映射关系的数学理论。线性映射的具象化就是矩阵，所以线性代数也是研究向量和矩阵的代数理论。

## 2 集合、空间和线性空间

集合：无序无结构，没有定义运算规则的元素集，如实数域R；

空间：有序含有某些结构（如运算法则）的集合，如实数轴R^1^；

线性空间：定义了加法和数乘的向量空间；

线性映射：从线性空间$S_{in}$ 到线性空间$S_{out}$ 的映射 $f$，若满足可加性和比例性：
$$
f(a_1 + a_2) = f(a_1) + f(a_2) \\
f(ka) = kf(a)
$$
则称$f$为线性映射。

例如，若映射$f（x） = kx + b$ 由于$f（x_1） + f(x_2) = kx_1 + kx_2 + 2b ≠ f(x_1+x_2)$ ，故其不能算线性映射。

二元函数：$z = f(x, y)$ 可视为二维平面向量$(x, y)$ 到一维向量$z$ 的线性映射。

两个二元线性函数组可视为二维向量到二维向量的线性映射:
$$
\{ \begin{array}  { l  }  { z = k_1 x + k_2 y } \\ { t = k_3  x + k_4 y } \end{array} => \left( \begin{array}  { l  }  { z } \\ { t } \end{array} \right) = \left[ \begin{array}  { l  }  { k_1 \   k_2 } \\ { k_3\ k_4 } \end{array} \right]\left( \begin{array}  { l  }  { x } \\ { y } \end{array} \right)
$$


$m$个$n$元线性函数组所确定的$m×n$ 矩阵确定了一个$n$ 维空间向量到$m$维空间向量的线性映射。
$$
y = f(x) = Kx
$$
其中，
$$
y = f(x) = \left( \begin{array}  { l  }  { y_1 } \\ { ... } \\{y_m} \end{array} \right) \\
K = \left[ \begin{array}  { l  }  { k_{11} \  \  k_{12} \  \ k_{1n} } \\ {...} \ \ \ {...} \ \ \ {...} \\{ k_{m1}\ \ k_{m2}\  \ k_{mn} } \end{array} \right] \\ x = \left( \begin{array}  { l  }  { x_1 } \\ { ... } \\{x_n} \end{array} \right)
$$
