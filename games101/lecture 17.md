[Manuka | Weta Digital](https://www.wetafx.co.nz/research-and-tech/technology/manuka/) 渲染器Renderer，支持40种材质。

以前只有Blinn Phong的时候，通过非物理的方式模拟出各种材质。

Material == BRDF 决定光如何被反射

## 漫反射、镜面反射、折射材质

-   Diffuse / Lambertian Material (BRDF) 漫反射材质
    
   ![[Pasted image 20221008102831.png]]
    
    $$ \begin{aligned} L_{o}\left(\omega_{o}\right) &=\int_{H^{2}} f_{r} L_{i}\left(\omega_{i}\right) \cos \theta_{i} \mathrm{d} \omega_{i} \\ &=f_{r} L_{i} \int_{H^{2}} \cos \theta_{i} \mathrm{d} \omega_{i} \\ &=\pi f_{r} L_{i} \end{aligned} $$
    
    -   能量守恒：进出的irradiance相同（总量）
    -   漫反射：出的radiance均匀→ $f_r = c$ （常量）
    -   假设入射光和出射光都是均匀的→ $L_i = L_o$
    
    $$ f_{r}=\frac{\rho}{\pi} $$
    
    -   ρ: albedo(反射率), 0~1（完全不吸收能量）
-   Glossy material (BRDF)
    
    ![[Pasted image 20221008103347.png]]
    
    -   Copper, Aluminum
-   Ideal reflective / refractive material (BSDF * )
    
   ![[Pasted image 20221008103443.png]]
    
    -   玻璃、水……
        
        -   折射光可被部分吸收
        -   反射定律：
        
        ![[Pasted image 20221008103538.png]]
        
        $$ \theta=\theta_{o}=\theta_{i} $$
        
        方位角：
        
        $$ \phi_{o}=\left(\phi_{i}+\pi\right) \bmod 2 \pi $$
        
        总结：
        
        $$ \begin{aligned} &\omega_{o}+\omega_{i}=2 \cos \theta \vec{n}=2\left(\omega_{i} \cdot \vec{n}\right) \vec{n}\\ &\omega_{o}=-\omega_{i}+2\left(\omega_{i} \cdot \vec{n}\right) \vec{n} \end{aligned} $$
        
    -   Specular Refraction折射
        
        -   白光分解成彩虹：折射率不同
        -   Caustics (不合适的翻译：焦散，因为只有聚焦才能被看到)
        -   折射定律（Snell’s Law）：
        
        ![[Pasted image 20221008103723.png]]
        
        -   全反射：折射不可能发生的情况下，当入射介质折射率>出射介质折射率时可能发生。
            -   Snell’s Window / Circle: 在水下往上看只能看到锥形的一片区域有光
    -   BSDF（散射） = BRDF（反射） + BTDF（折射）
        
    -   Fresnel Reflection / Term （菲涅⽿项）
        
        -   Reflectance depends on incident angle (and polarization of light)
            
        -   和normal法线方向越接近，越少光被反射
             ![[Pasted image 20221008104832.png]]
        -   精确计算：需要考虑极化情况
            
        -   近似：Schlick’s approximation
            
        
        $$ \begin{aligned} R(\theta) &=R_{0}+\left(1-R_{0}\right)(1-\cos \theta)^{5} \\ R_{0} &=\left(\frac{n_{1}-n_{2}}{n_{1}+n_{2}}\right)^{2} \end{aligned} $$
        

## Microfacet Material 微表面材质

基于如下假设：离得足够远的时候，微小的东西往往看不见，看见的是最后汇聚起来总体的样子

Rough surface

-   Macroscale: flat & rough
-   Microscale: bumpy & specular

Individual elements of surface act like mirrors

-   Known as Microfacets
-   Each microfacet has its own normal

Microfacet BRDF 微表面模型

-   the distribution of microfacets’ normals 法线
    
-   Concentrated <==> glossy
    ![[Pasted image 20221008105519.png]]
-   Spread <==> diffuse
    ![[Pasted image 20221008105533.png]]
-   BRDF计算：
    
    $$ f(\mathbf{i}, \mathbf{o})=\frac{\mathbf{F}(\mathbf{i}, \mathbf{h}) \mathbf{G}(\mathbf{i}, \mathbf{o}, \mathbf{h}) \mathbf{D}(\mathbf{h})}{4(\mathbf{n}, \mathbf{i})(\mathbf{n}, \mathbf{o})} $$
    
    -   $\mathbf{F}(\mathbf{i}, \mathbf{h})$: Fresnel term
    -   $\mathbf{G}(\mathbf{i}, \mathbf{o}, \mathbf{h})$: shadowing-masking term，微表面互相遮挡的损耗
        -   几乎和表面平行的光线方向：Grazing Angle，损耗很大
    -   $\mathbf{D}(\mathbf{h})$: 基于h的法线Distribution, h是half vector，
-   效果十分强大
    
-   State-of-the-art
    
-   PBR：Physically Based Rendering / Shading
    
-   缺点
    
    -   Diffuse比较少，有时需要手动加
-   是统称，有很多不同的模型
    

## Isotropic(各向同性) / Anisotropic Materials (BRDFs)

电梯间的条状高光→磨过的表面，各向异性Anisotropic

-   关键区别**Key: directionality of underlying surface**

![[Pasted image 20221008105930.png]]

反映到BRDF上，各向异性的BRDF有如下性质：

$$ f_{r}\left(\theta_{i}, \phi_{i} ; \theta_{r}, \phi_{r}\right) \neq f_{r}\left(\theta_{i}, \theta_{r}, \phi_{r}-\phi_{i}\right) $$

-   方位角不一样时，BRDF不保持一致(Reflection depends on azimuthal angle)
-   锅底 → 辐射状高光 
-   Nylon（编织）, Velvet（天鹅绒，可以人为造成各向异性）

BRDF的属性：

-   非负Non-negativity：描述能量分布
    
-   线性Linearity：可加，组合
    
-   可逆性(Reciprocity principle)：交换入射和出射，结果一致
    
-   能量守恒Energy conservation：能量要么一致，要么变小（被吸收），收敛
    
-   各向同性：
    
    $$ f_{r}\left(\theta_{i}, \phi_{i} ; \theta_{r}, \phi_{r}\right) = f_{r}\left(\theta_{i}, \theta_{r}, \phi_{r}-\phi_{i}\right) $$
    
    从可逆性可得：
    
    $$ f_{r}\left(\theta_{i}, \theta_{r}, \phi_{r}-\phi_{i}\right)=f_{r}\left(\theta_{r}, \theta_{i}, \phi_{i}-\phi_{r}\right)=f_{r}\left(\theta_{i}, \theta_{r},\left|\phi_{r}-\phi_{i}\right|\right) $$
    
    于是不用考虑方位角的绝对值，便于储存
    

## Measuring BRDFs

Motivation:

-   Avoid need to develop / derive models 不用费力推模型
    -   Automatically includes all of the scattering effects present
-   Can accurately render with real-world materials
    -   Useful for product design, special effects, ...
-   实际和推算出来的经常会有很大差距

方法：

-   Image-Based BRDF Measurement
    
    给定入射出射，参数十分精确，直接测量，枚举
    
   ![[Pasted image 20221008110102.png]]
    
    -   实际存在：UCSD的gonioreflectometer
    -   四维，维度爆炸 → 各向同性：三维 → 可逆性：砍掉一半
    -   或者采样部分，剩余插值（猜测）
    -   挑战：
        -   Accurate measurements at grazing angles
            -   Important due to Fresnel effects
        -   Measuring with dense enough sampling to capture high frequency specularities
        -   Retro-reflection Spatially-varying reflectance,

## 存储 BRDF

Desirable qualities

-   Compact representation
-   Accurate representation of measured data
-   Efficient evaluation for arbitrary pairs of directions
-   Good distributions available for importance sampling

一种方式：Tabular Representation

-   Store regularly-spaced samples in $\left(\theta_{i}, \theta_{r},\left|\phi_{r}-\phi_{i}\right|\right)$
-   Better: reparameterize angles to better match specularities
-   Generally need to resample measured values to table
-   Very high storage requirements
-   实例：MERL BRDF Database [Matusik et al. 2004] 90 * 90 * 180 measurements