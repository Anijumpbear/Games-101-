# 19 - Cameras, Lenses and Light Fields

Imaging = Synthesis + Capture

-   Synthesis: raster | ray tracing 用合成的方法来成像
-   Capture: 捕捉真实世界来成像
    -   最典型：相机
    -   Transient Imaging: 研究光在极短时间内的传播
    -   Computational Photography: 计算成像学

相机的原理

-   小孔成像
    -   针孔相机 Pinhole Camera
        -   无虚化
        -   全部都是清晰的
-   Lens 透镜成像

Shutter 快门，控制光进入相机

Sensor 传感器，Accumulates Irradiance During Exposure

Why Not Sensors Without Lenses?

-   任何一个点都可能收集到不同方向过来的光
-   the sensor records irradiance

针孔相机 Pinhole Camera

# Field of View (FOV) 视场

$$ \mathrm{FOV}=2 \arctan \left(\frac{h}{2 f}\right) $$

-   h: 传感器高度
-   f: 焦距，传感器与透镜的距离

通常以35mm(36 x 24mm)格式胶片为基准，通过定义焦距的方式来定义FOV：

-   17mm is wide angle 104° 广角镜头
-   50mm is a “normal” lens 47°
-   200mm is telephoto lens 12°

同样大小的传感器，焦距越大，视场越窄

同样焦距，传感器越大，视场越宽

同样视场，焦距和传感器等比

传感器Sensor和胶片Film不一定一样

-   平时两者等价
-   在渲染器里面，Sensor收集irradiance，Film决定存成什么样的格式

![[Pasted image 20221008132408.png]]

# Exposure 曝光

H=TxE

Exposure = time x irradiance

Exposure time (T)

-   Controlled by shutter

Irradiance (E)

-   Power of light falling on a unit area of sensor
-   Controlled by lens aperture（光圈） and focal length（焦距）

Aperture size 光圈大小

-   Change the f-stop by opening / closing the aperture (if camera has iris control)
-   仿生学设计，模拟瞳孔
-   F-Number (F-Stop)

Shutter speed 快门速度

-   Change the duration the sensor pixels integrate light
-   越快，开放时间越短，相对越少光

ISO gain 感光度

-   后期处理
-   Change the amplification (analog and/or digital) between sensor values and digital image values
-   硬件上 调节传感器灵敏度 或者 软件上 调整数字信号
-   Third variable for exposure
-   Film: trade sensitivity for grain
-   Digital: trade sensitivity for noise
    -   Multiply signal before analog-to-digital conversion
    -   Linear effect (ISO 200 needs half the light as ISO 100) 线性
-   为什么会有噪声？
    -   光子数量少

![[Pasted image 20221008132427.png]]

F-Number (F-Stop) 曝光等级 - 光圈大小

-   数字越小，光圈越大
-   Written as FN or F/N. N is the f-number.
-   Informal understanding: the inverse-diameter of a round aperture 简单地理解为直径的倒数

Side Effect of Shutter Speed 快门速度过慢

-   Motion blur: handshake, subject movement
-   Doubling shutter time doubles motion blur
-   motion blur is not always bad! 表现快 or anti-aliasing~

Rolling shutter: different parts of photo taken at different times

-   物理快门并非瞬间打开，而是以某种方式“缓慢“打开的
-   会导致高速运动的物体的成像与预想不一致

进光量～F-Stop^2 / Shutter Speed

但是大光圈会有浅景深的效果，快门时间会导致运动模糊等效果，需要权衡

# Fast and Slow Photography

High-Speed Photography 每秒极高帧率

-   快门时间受限，
-   Normal exposure = extremely fast shutter speed x (large aperture and/or high ISO)

Long-Exposure Photography 延时摄影

-   快门时间很长
-   光圈变小

# Thin Lens Approximation

[](http://graphics.stanford.edu/courses/cs178-10/applets/gaussian.html)[http://graphics.stanford.edu/courses/cs178-10/applets/gaussian.html](http://graphics.stanford.edu/courses/cs178-10/applets/gaussian.html)

Real Lens Designs Are Highly Complex

-   目前的相机都用透镜组来控制成像
-   真实的透镜并不那么理想：无法将光线聚于一点 Aberrations
    -   Real plano-convex lens (spherical surface shape). Lens does not converge rays to a point anywhere.
    

Ideal Thin Lens – Focal Point

-   理想的透镜可将光聚于一点 - 焦点
-   (1) All parallel rays entering a lens pass through its focal point.
-   (2) All rays through a focal point will be in parallel after passing the lens.
-   (3) Focal length can be arbitrarily changed (in reality, yes! → 透镜组).

通过透镜组之间的相对位置变化来模拟焦距的改变

The (Gaussian) Thin Lens Equation:

$$ \frac{1}{f}=\frac{1}{z_{i}}+\frac{1}{z_{o}} $$

-   相似三角形证明

通过这个Thin Lens Approximation可以解释很多问题：

## 解释 Defocus Blur 虚化的原因

Computing Circle of Confusion (CoC) Size

![[Pasted image 20221008132535.png]]

各种距离固定情况下，CoC 与光圈大小成正比！

F-Number (a.k.a. F-Stop) 's Formal definition: The f-number of a lens is defined as **the focal length divided by the diameter of the aperture 焦距/光圈直径**

Common f-stops on real lenses: 1.4, 2, 2.8, 4.0, 5.6, 8, 11, 16, 22, 32

the absolute aperture diameter (A) can be computed by dividing focal length (f) by the relative aperture (N). → An f-stop of 2 is sometimes written f/2, 因为 A=f/2

$$ C=A \frac{\left|z_{s}-z_{i}\right|}{z_{i}}=\frac{f}{N} \frac{\left|z_{s}-z_{i}\right|}{z_{i}} $$

-   于是 更清楚 → N 要大 → 光圈要小

# Ray Tracing Ideal Thin Lenses

(One possible) Setup:

-   Choose sensor size, lens focal length and aperture size
-   Choose depth of subject of interest $z_o$
-   Calculate corresponding depth of sensor $z_i$ from thin lens equation

Rendering:

-   For each pixel x’ on the sensor (actually, film (胶片))
-   Sample random points x’’ on lens plane
-   You know the ray passing through the lens will hit x’’’ (because x’’’ is in focus, consider virtual ray (x’, center of the lens))
-   Estimate radiance on ray x’’ -> x’’’

![[Pasted image 20221008132554.png]]

## Depth of Field 景深

一次成像内，模糊的范围会因光圈大小而变化，而在focal plane肯定不会模糊。

Set circle of confusion as the maximum permissible blur spot on the image plane that will appear sharp under final viewing conditions

depth range in a scene where subject where the corresponding CoC is considered small enough

首先，当CoC（Circle of Confusion）小到一定程度，认为是清晰的

$z_o$附近的一段距离内都可以看作清晰（CoC小到一定程度），关键是这段距离有多长。→景深

光圈越小，景深范围越大

焦距越大，景深范围越小

[](http://graphics.stanford.edu/courses/cs178/applets/dof.html)[http://graphics.stanford.edu/courses/cs178/applets/dof.html](http://graphics.stanford.edu/courses/cs178/applets/dof.html)
![[Pasted image 20221008132625.png]]
# 光场 Light Field / Lumigraph

两个词指同一东西，属于历史遗留问题，是由两个组独立几乎同时发现的同样东西

The Plenoptic Function 全光函数(Adelson & Bergen): the set of all things that we can ever see

-   Grayscale snapshot：$\boldsymbol{P}(\theta, \phi)$ is intensity of light
    
    -   Seen from a single view point
    -   At a single time
    -   Averaged over the wavelengths of the visible spectrum → 没有具体的颜色
-   Color snapshot：$\boldsymbol{P}(\theta, \phi, \lambda)$ is intensity of light
    
    -   Seen from a single view point
    -   At a single time
    -   As a function of wavelength 彩色
-   Movie：
    
    $$ \boldsymbol{P}(\theta, \phi, \lambda, t) $$
    
    -   is intensity of light
    -   Seen from a single view point
    -   Over time
    -   As a function of wavelength 彩色
-   Holographic movie 全息电影 or The Plenoptic Function
    
    $$ \boldsymbol{P}\left(\theta, \phi, \lambda, t, V_X,V_Y,V_Z\right) $$
    
    -   is intensity of light
    -   Seen from ANY viewpoint 任何位置看都可以
    -   Over time
    -   As a function of wavelength 彩色
    -   Can reconstruct every possible view, at every moment, from every position, at every wavelength
    -   Contains every photograph, every movie, everything that anyone has ever seen! it completely captures our visual reality!

要实现这样一个函数，需要：空间中任意一点的全方向光线信息

Ray 光线：

$$ \boldsymbol{P}\left(\theta, \phi, V_X,V_Y,V_Z\right) $$

-   5D: 3D position + 2D direction

Ray Reuse

-   4D: 2D direction + 2D position（表面为二维）
-   non-dispersive medium

要记录一个物体向四周展示的样子，只需要记录包围盒上表面各个点往各个方向发射的光线的信息：The surface of a cube holds all the radiance information due to the enclosed object. → 光场

光场记录的方式（四维）：

-   2D position + 2D direction
-   2D position + 2D position：用两个面上两个点来定义光线的方向→2 plane parameterization (u,v) → (s,t)
-   固定(u,v), 所有的(s,t)组成一张image，显示从(u,v)点看到的外部世界的样子。
    -   Stanford camera array
-   固定(s,t), 所有的(u,v)组成一张image，显示同一个点上从不同方向看过去的样子
    -   Integral Imaging (苍蝇的眼睛Lenslets)
    -   从同一个点，将不同方向打过来的光线经过折射记录在不同的位置上
    -   Spatially-multiplexed light field capture using lenslets: Impose fixed trade-off between spatial and angular resolution
    -   将单单记录Irradiance变成了不同方向的Radiance

## Light Field Camera

-   Lytro: founded by Prof. Ren Ng (UC Berkeley)
    -   Microlens design
        
    -   Most significant function
        
        -   Computational Refocusing
        
        (virtually changing focal length & aperture size, etc. after taking the photo)
        
    -   Each pixel (irradiance) is now stored as a block of pixels (radiance)
        
        -   恢复：A simple case — always choose the pixel at the bottom of each block
            -   Then the central ones & the top ones
            -   Essentially “moving the camera around”
        -   Computational / digital refocusing
            -   Same idea: visually changing focal length, picking the refocused ray directions accordingly
    -   The light field contains everything
        
-   缺点
    -   Insufficient spatial resolution (same film used for both spatial and directional information) 分辨率不足
    -   High cost (intricate designation of microlenses) 高成本、难设计
    -   Computer Graphics is about trade-offs 调节更灵活 分辨率更高