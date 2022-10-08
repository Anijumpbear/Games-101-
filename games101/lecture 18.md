# Lec 18 - Advanced Topics in Rendering

## Advanced Light Transport

-   Unbiased light transport methods
    -   Bidirectional path tracing (BDPT)
    -   Metropolis light transport (MLT)
-   Biased light transport methods
    -   Photon mapping
    -   Vertex connection and merging (VCM)
-   Instant radiosity (VPL / many light methods)
    -   间接光表示为很多小光源

Biased vs. Unbiased Monte Carlo Estimators

-   无偏：An unbiased Monte Carlo technique does not have any systematic error
    -   无论取多少样本，期望永远是对的
-   有偏：其他情况
    -   One special case, the expected value converges to the correct value as infinite #samples are used — consistent 有偏，但能收敛到无偏

### Bidirectional path tracing (BDPT 双向路径追踪)

-   Traces sub-paths from both the camera and the light (半路径)
-   Connects the end points from both sub-paths 半路径末端互相连接，形成通路
-   好处：Suitable if the light transport is complex on the light’s side
    -   比如，光源第一跳大多是diffuse
-   缺点：Difficult to implement & quite slow 能做出来基本能自己写渲染器了

### Metropolis light transport (MLT)

-   A Markov Chain Monte Carlo (MCMC) application
    -   马尔可夫链帮助采样
        -   马尔可夫链：根据当前样本，根据一个概率分布，生成下一个相近的样本
    -   Jumping from the current sample to the next with some PDF
-   可以做到以任意函数为pdf生成样本
-   Key idea: Locally perturb an existing path to get a new path 有一个路径的情况下，可以生成相似的路径
-   好处：Very good at locally exploring difficult light paths 有了种子，就能找到更多相似的
    -   Caustics, Indirect Light Source
-   缺点：
    -   Difficult to estimate the convergence rate
        -   Monte Carlo可以估计Variance，可以量化
    -   Does not guarantee equal convergence rate per pixel
    -   So, usually produces “dirty” results 看上去比较脏
    -   Therefore, usually not used to render animations

### Photon Mapping (光子映射)

-   A biased approach & A two-stage method
-   Very good at handling Specular-Diffuse-Specular (SDS) paths and generating caustics

很多种实现方法，这里是其中一种：

Photon Mapping — Approach (variations apply)

-   Stage 1 — photon tracing
    -   光源出发直到diffuse，Emitting photons from the light source, bouncing them around, finally recording photons on diffuse surfaces
-   Stage 2 — photon collection (final gathering)
    -   摄像机出发sub-paths,直到diffuse,   Shoot sub-paths from the camera, bouncing them around, until they hit diffuse surfaces
-   Calculation — local density estimation 局部光子密度估计
    -   Idea: areas with more photons should be brighter
    -   For each shading point, find the nearest N photons (通过树状结构实现算法，N是固定的). Take the surface area they over 面积计算，然后光子密度=光子数量/面积
    -   光子数量少：面积大，噪声大；光子数量大：模糊
-   模糊是因为有偏
    -   Local Density estimation dN / dA != ΔN / ΔA 光子密度估计在数量趋向无限时才与真正光子密度相等，所以biased but consistent!

有偏和无偏的实用辨别方法：

-   在渲染中：Biased有偏 == blurry模糊
-   一致的Consistent == not blurry with infinite #samples

[如何理解 (un)biased render？ - 知乎](https://www.zhihu.com/question/26683585)


### Vertex Connection and Merging (VCM)

-   A combination of BDPT and Photon Mapping
-   Key idea: Let’s not waste the sub-paths in BDPT if their end points cannot be connected but can be merged, but Use photon mapping to handle the merging of nearby “photons”

![[Pasted image 20221008123255.png]]

-   比如，$x_2$和$x^{*}_{2}$ 在同一个面上，但是没有重合，按照BDPT，这种就是浪费
-   但是VCM决定利用这种情况，把其中一半光路转化成光子，进行Photon Mapping一样的计算

### Instant Radiosity (IR)

-   aka many-light approaches 很多光源的方法
-   Key idea: Lit surfaces can be treated as light sources 被照亮的表面就像是光源
-   模拟从光源发出光线，打到的地方相当于二级光源。如果此时Sample某个场景点的颜色，那么遍历这些二级光源，叠加计算即可
-   Shoot light sub-paths and assume the end point of each sub-path is a Virtual Point Light (VPL), Then Render the scene as usual using these VPLs
-   Pros: fast and usually gives good results on diffuse scenes
-   Cons:
    -   Spikes will emerge when VPLs are close to shading points
    -   Cannot handle glossy materials

工业界：Path Tracing，不高端，但可靠

## Advanced Appearance Modeling

Appearance → Material → BRDF

Non-surface models

-   Participating media
-   Hair / fur / fiber (BCSDF)
-   Granular material

Surface models

-   Translucent material (BSSRDF)
-   Cloth
-   Detailed material (non-statistical BRDF)

Procedural appearance

### Non-Surface Models

Participating Media (散射介质)：Fog, Cloud ...

-   At any point as light travels through a participating medium, it can be (partially) absorbed and scattered. （部分）吸收和散射
-   散射：Use **Phase Function(相位函数)** to describe the angular distribution of light scattering at any point x within participating media. 规定如何散射
	![[Pasted image 20221008125117.png]]
-   如何渲染Rendering:
    -   Randomly choose a direction to bounce
    -   Randomly choose a distance to go straight
    -   At each ‘shading point’, connect to the light

散射介质也有可能是巧克力酱、手

#### Hair Appearance

关键点：光线和曲线的作用

![[Pasted image 20221008125848.png]]

-   有两种高光：无色 & 有色高光，如何形成？
    -   无色高光
    -   有色高光

Kajiya-Kay Model：简单模型

-   一根光线，打到圆柱（头发）上，散射出一个圆锥上，同时向四面八方散射，像是Diffuse+Specular想加
-   效果不好，很假

![[Pasted image 20221008125956.png]]

Marschner Model：广泛使用

-   把头发看作Glass-like cylinder，有色素，会吸收能量
-   3 types of light interactions: R, TT, TRT (R: reflection, T: transmission)
-   possible extension: TRRT
-   一部分直接反射（R）
-   一部分穿进头发，再穿出（TT）
-   一部分穿进，内部反射，再穿出TRT
-   效果不错
-   只是单次散射
![[Pasted image 20221008130108.png]]
![[Pasted image 20221008130135.png]]


#### Fur Appearance

不能直接把头发模型用到动物毛发上，两者有差别
![[Pasted image 20221008130209.png]]

动物毛发髓质(Medulla)很大，头发忽略了不明显，但是动物毛发忽略之后很明显

Double Cylinder Model：描述了头发和髓质的双层结构

-   R，TT，TRT
-   TTs，TRTs：经过髓质，被散射过的光
![[Pasted image 20221008130304.png]]
应用：

-   [War for the Planet of the Apes. 2017 movie] (2018 Oscar Nominee for Best Visual Effects)
-   [The Lion King (HD). 2017 movie] (2019 Oscar Nominee for Best Visual Effects)

![[Pasted image 20221008130402.png]]

#### Granular Material 颗粒状材质

Can we avoid explicit modeling of all granules?

-   Yes with procedural definition.

目前还没有很好的渲染优化

### Surface Models

#### Subsurface Scattering 次表面散射, BSSRDF

Translucent: 不仅仅是半透明，光线在内部还会有折射，比如玉石，水母，牛奶，人耳

物理上：Subsurface Scattering 次表面散射

-   Visual characteristics of many surfaces caused by light exiting at different points than it enters
-   Violates a fundamental assumption of the BRDF → BSSRDF

BSSRDF: generalization of BRDF; exitant radiance at one point due to incident differential irradiance at another point: $S\left(x_{i}, \omega_{i}, x_{o}, \omega_{o}\right)$ 进来和出去的点不一定一样

Generalization of rendering equation: integrating over all points on the surface and all directions 对表面其他地方进入的光线也要考虑，过于复杂

$$ L\left(x_{o}, \omega_{o}\right)=\int_{A} \int_{H^{2}} S\left(x_{i}, \omega_{i}, x_{o}, \omega_{o}\right) L_{i}\left(x_{i}, \omega_{i}\right) \cos \theta_{i} \mathrm{d} \omega_{i} \mathrm{d} A $$

→ Dipole Approximation [Jensen et al. 2001]: Approximate light diffusion by introducing two point sources. 用两个光源模拟次表面散射

BSSRDF: Application
![[Pasted image 20221008131046.png]]
-   真实的皮肤
-  [https://cgelves.com/10-most-realistic-human-3d-models-that-will-wow-you/](https://cgelves.com/10-most-realistic-human-3d-models-that-will-wow-you/)

#### Cloth: Fiber

布：A collection of twisted fibers! 由纤维缠绕而成
![[Pasted image 20221008131134.png]]
-   每一根纱线是由纤维缠绕而成
-   每一股毛线是由纱线缠绕而成
-   规模很恐怖

Render as Surface

-   Given the weaving pattern, calculate the overall behavior
-   Render using a BRDF，根据不同的织法，给不同的BRDF
-   但是布料并不仅仅是表面，天鹅绒那样的效果无法展现

Render as Participating Media

-   Properties of individual fibers & their distribution -> scattering parameters
-   空间中分布的体积→细小的格子，对每个格子里布料的性质进行采样，转化成对光线的渲染 （像对云的渲染那样）计算量很恐怖

Render as Actual Fibers

-   Render every fiber explicitly!
-   计算量更恐怖

#### Detailed Appearance

Not looking realistic, 因为表面无噪太过完美

回顾Microfacet BRDF

-   Surface = Specular microfacets + statistical normals
-   法线分布(NDF)用了正态分布，其实没有那么完美
-   把法线分布改的复杂一点，就会获得更复杂的结果
-   法线贴图200k x 200k，获得很好的效果，但是运算量爆炸（甚至一张图要一个月）
-   这是因为在法线分布复杂的情况下，很难建立valid的光线通路（光源→表面→摄像机）

Solution: BRDF over a pixel
![[Pasted image 20221008131839.png]]
-   一个像素对应了一块微表面，把整块范围内的各向法线分布整合起来获得（P-NDF），以此简化计算
-   小范围的P-NDF会有很多奇妙的样子

Application:

-   Detailed / Glinty Material
-   海绵波光粼粼的效果

#### Recent Trend: Wave Optics

当物体小到和光线波长相当的时候, 不可忽略光的波动性

![[Pasted image 20221008132158.png]]

-   波动光学计算BRDF
    -   结果与几何光学类似，但不连续（干涉导致），不同波长也不一样

### Procedural Appearance 程序化生成材质

-   Explicitly or Implicitly 不一定需要真的生成，可以查询

问题：三维模型，三维材质，存储量爆炸

Can we define details without textures?

-   Yes! Compute a noise function on the fly.
-   3D noise -> internal structure if cut or broken
-   Thresholding (noise -> binary noise)

Complex noise functions can be very powerful.

-   Perlin Noise
    -   生成地形
    -   木头的三维生成
-   ...

Houdini: 程序化生成材质(Explicitly)