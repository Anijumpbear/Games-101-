# Lec 14(2)-15(1) - 光的传播理论, Basic Radiometry 辐射度量学, 路径追踪与全局光照

Whitted Style Ray Tracing 无法保证正确性

辐射度量学：精准度量光的一系列物理量 → Physically Based

-   the basics of Path Tracing
-   Measurement system and units for illumination
-   Accurately measure the spatial properties of light
    -   New terms: Radiant flux, intensity, irradiance, radiance
-   Perform lighting calculations in a physically correct manner

## Radiant Energy and Flux (Power)
微分立体角![[Pasted image 20220929104702.png]]
辐照度衰减
![[Pasted image 20220929104859.png]]
基础物理量

-   Radiant energy: 能量，the energy of electromagnetic radiation
    -   符号：Q
    -   单位：J（Joule焦耳）
-   Radiant flux (power): the energy emitted, reflected, transmitted or received, **per unit time**. （单位时间能量 → 功率）
    -   符号：ϕ （phi）
    -   单位：W（Watt），lm（lumen流明）
    -   单位时间 很重要
-   Flux: number of photons flowing through a sensor in unit time

Important Light Measurements of Interest

-   Radiant(luminous) **Intensity**: the power per unit solid angle (立体角) emitted by a point light source.
    
    -   符号定义：
    
    $$ I(\omega) = \frac{\mathrm{d} \Phi}{\mathrm{d} \omega} $$
    
    -   单位：W/sr，lm/sr=cd(candela坎德拉)
        
    -   立体角
        
        -   弧度：弧长/半径
        -   弧度在三维的延伸→立体角：面积/半径^2，
        
        $$ \Omega=\frac{A}{r^{2}} $$
        
        -   单位：sr, steradians
        -   球面=4pi sr
        
        $$ \mathrm{d} \omega=\frac{\mathrm{d} A}{r^{2}}=\sin \theta \mathrm{d} \theta \mathrm{d} \phi $$
        
    -   方向性
        
        -   用 $\omega$ 来表示方向
    -   点光源：
        
    
    $$ I = \frac{\phi}{4\pi} $$
    
-   Irradiance: power per unit area (perpendicular/ projected)
    
    -   Light Falling On A Surface
    
    $$ E(\mathbf{x}) \equiv \frac{\mathrm{d} \Phi(\mathbf{x})}{\mathrm{d} A} $$
    
    -   单位：W/m^2 | lm/m^2 = lux
    -   需要注意，是垂直方向
        -   Lambert’s Cosine Law
        -   地球的四季
        -   Falloff: 点光源的能量散布
    -   无方向性
-   **Radiance: power per unit solid angle, per projected unit area.**
    
    -   describes the distribution of light in an environment
    -   Light Traveling Along A Ray
    
    $$ L(\mathrm{p}, \omega) \equiv \frac{\mathrm{d}^{2} \Phi(\mathrm{p}, \omega)}{\mathrm{d} \omega \mathrm{d} A \cos \theta} $$
    
    -   某个单位面积往某个单位立体角辐射的能量密度
        
    -   Radiance: **Irradiance** per solid angle
        
    -   Radiance: **Intensity** per projected unit area
        
    -   有方向性
        
    -   Incident radiance is the irradiance per unit solid angle arriving at the surface. 某个小面接受来自某个方向的光线
        
        $$ L(\mathrm{p}, \omega)=\frac{\mathrm{d} E(\mathrm{p})}{\mathrm{d} \omega \cos \theta} $$
        
    -   Exiting surface radiance is the intensity per unit projected area leaving the surface. 某个小面发出向某个方向的光线
        
        $$ L(\mathrm{p}, \omega)=\frac{\mathrm{d} I(\mathrm{p}, \omega)}{\mathrm{d} A \cos \theta} $$
        

Irradiance和Radiance之间的区别就在于是否有方向性

-   Irradiance: total power received by area dA 四面八方的光线积分起来
-   Radiance: power received by area dA from “direction” dω

$$ \begin{aligned} d E(\mathrm{p}, \omega) &=L_{i}(\mathrm{p}, \omega) \cos \theta \mathrm{d} \omega \\ E(\mathrm{p}) &=\int_{H^{2}} L_{i}(\mathrm{p}, \omega) \cos \theta \mathrm{d} \omega \end{aligned} $$

-   H^2：半球面

## Bidirectional Reflectance Distribution Function (BRDF) 双向反射分布函数

**BRDF描述了从某个方向入射到一个点上的光线的能量会怎么反射，在不同的反射方向上会各分布多少能量**

反射的理解：光线打到某个点，（被吸收了）然后反弹（发出）到其他地方

Radiance from direction ωi turns into the power E that dA（某个点） receives, Then power E will become the radiance to any other direction ωr

![[Pasted image 20220929105954.png]]

能量一般指功率，即单位时间内的能量。

某个点接受/发射光线总能量：Irradiance

某个点从某个方向接受/向某个方向发射光线能量：radiance

-   Differential irradiance incoming:

$$ d E\left(\omega_{i}\right)=L\left(\omega_{i}\right) \cos \theta_{i} d \omega_{i} $$

-   Differential radiance exiting:

$$ d L_{r}\left(\omega_{r}\right) $$

BRDF的几个细节：

-   针对一个输入源
-   描述不同方向的输出
-   定义了不同材质

BRDF：represents how much light is reflected into each outgoing direction dL r (! r ) from each incoming direction

![[Pasted image 20220929110028.png]]

$$ f_{r}\left(\omega_{i} \rightarrow \omega_{r}\right)=\frac{\mathrm{d} L_{r}\left(\omega_{r}\right)}{\mathrm{d} E_{i}\left(\omega_{i}\right)}=\frac{\mathrm{d} L_{r}\left(\omega_{r}\right)}{L_{i}\left(\omega_{i}\right) \cos \theta_{i} \mathrm{d} \omega_{i}}\left[\frac{1}{\mathrm{sr}}\right] $$

## The Reflection Equation 反射方程

-   针对一个输出源（着色点），积分所有方向输入源（光照）的BRDF，获得最后的输出

$$ L_{r}\left(\mathrm{p}, \omega_{r}\right)=\int_{H^{2}} f_{r}\left(\mathrm{p}, \omega_{i} \rightarrow \omega_{r}\right) L_{i}\left(\mathrm{p}, \omega_{i}\right) \cos \theta_{i} \mathrm{d} \omega_{i} $$

Challenge: Recursive Equation

-   不只有光源才是输入源，其他物体的反射光也有可能是输入源
-   递归的计算方式，计算量爆炸！

## The Rendering Equation 渲染方程（绘制方程）

**Rendering Equation (Kajiya 86) 跨时代**

Adding an Emission term to the reflection equation to make it general!

$$ L_{o}\left(p, \omega_{o}\right)=L_{e}\left(p, \omega_{o}\right)+\int_{\Omega^{+}} L_{i}\left(p, \omega_{i}\right) f_{r}\left(p, \omega_{i}, \omega_{o}\right)\left(n \cdot \omega_{i}\right) \mathrm{d} \omega_{i} $$

-   包括了物体自己会发光的情况，包括了所有光线的传播情况
-   assume that all directions are pointing outwards!
-   $H^2$和$\Omega^{+}$ 都代表半球
-   怎么解这个方程呢？下节课

单个点光源：
![[Pasted image 20220929110101.png]]

多个点光源：加起来
![[Pasted image 20220929110118.png]]

还有面光源：积分起来
![[Pasted image 20220929110141.png]]

考虑其他物体反射的光线（把光源也包括在内了）：→递归
![[Pasted image 20220929110155.png]]
$$ L_{r}\left(x, \omega_{r}\right)=L_{e}\left(x, \omega_{r}\right)+\int_{\Omega} L_{r}\left(x^{\prime},-\omega_{i}\right) f\left(x, \omega_{i}, \omega_{r}\right) \cos \theta_{i} d \omega_{i} $$

这个方程是 Fredholm Integral Equation of second kind [extensively studied numerically] with canonical form → 简写为如下形式

$$ I(u)=\theta(u)+\int l(v)K(u,v)dv $$

通过算符的抽象还可极度简化成如下形式：L = E + KL （K：反射算符）其中L为未知数

-   Can be discretized to a simple matrix equation [or system of simultaneous linear equations] (L, E are vectors, K is the light transport matrix)
![[Pasted image 20220929110250.png]]
![[Pasted image 20220929110258.png]]

-   光栅化的着色过程只有直接光照（间接光照需要间接计算）
-   全局光照：直接和间接光照的集合
    -   会收敛到一个亮度

如何解全局光照方程？Monte Carlo Path Tracing (Lec 16)