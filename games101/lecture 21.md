# Lec 21 - Animation

- 动画是一种信息传递的工具
    -   美学经常比技术重要
-   是模型的延伸→连续性
    -   Represent scene models as a function of time
-   输出：sequence of images that when viewed sequentially provide a sense of motion
    -   电影：24FPS
    -   视频：30FPS、29.994FPS
    -   VR：90FPS （不晕的基础要求）

# History

最早：狩猎鹿的动画(Shahr-e Sukhteh, Iran 3200 BCE)

圆盘旋转：(Phenakistoscope, 1831)

第一部Film：Edward Muybridge, “Sallie Gardner” (1878)

First Hand-Drawn Feature-Length (>40 mins) Animation：Disney, “Snow White and the Seven Dwarfs” (1937)

First Digital-Computer-Generated Animation：Ivan Sutherland, “Sketchpad” (1963) – Light pen, vector display

Early Computer Animation：Ed Catmull & Frederick Parke, “Computer Animated Faces” (1972)

Digital Dinosaurs!：Jurassic Park (1993)

First CG Feature-Length Film：Pixar, “Toy Story” (1995) （光栅化）

Computer Animation - 10 years ago：Sony Pictures Animation, “Cloudy With a Chance of Meatballs” (2009)

Computer Animation - last year：Walt Disney Animation Studios, “Frozen 2” (2019)

# Keyframe animation关键帧动画

-   Animator (e.g. lead animator) creates keyframes 关键帧
-   Assistant (person or computer) creates in-between frames (“tweening”) 渐变帧

## 关键的技术难点 - Interpolation 插值

-   Linear interpolation usually not good enough
-   Recall splines for smooth / controllable interpolation

B样条……

# Physical Simulation物理模拟

模拟、仿真：推导、实现公式，模拟出物体应该怎么变化

例子：布料模拟、流体模拟

## 质点弹簧系统 Mass Spring System: Example of Modeling a Dynamic System

Example: Mass Spring Rope, Hair, Mass Spring Mesh

-   A Simple Idealized Spring
    
    -   没有初始长度
    -   随着拉力线性增长/缩短，线性系数是spring coefficient: stiffness
    -   Force pulls points together
    -   Strength proportional to displacement (Hooke’s Law)
    -   问题：长度会倾向于0
-   Non-Zero Length Spring
    
    -   初始长度Rest length不为零
    -   Problem: oscillates forever 永远震荡
    
    $$ \boldsymbol{f}_{a \rightarrow b}=k_{s} \frac{\boldsymbol{b}-\boldsymbol{a}}{\|\boldsymbol{b}-\boldsymbol{a}\|}(\|\boldsymbol{b}-\boldsymbol{a}\|-l) $$
    

Dot Notation for Derivatives：

$$ \begin{aligned} &\boldsymbol{x}\\ &\dot{\boldsymbol{x}}=\boldsymbol{v}\\ &\ddot{\boldsymbol{x}}=\boldsymbol{a} \end{aligned} $$

-   Introducing Energy Loss
    
    -   Simple motion damping 阻尼
        
        $$ \boldsymbol{f}=-k_{d} \dot{\boldsymbol{b}} $$
        
    -   Behaves like viscous drag on
        
    -   Slows down motion in the direction of velocity
        
    -   $k_d$ is a damping coefficient
        
    -   问题：Slows down all motion
        
        -   Want a rusty spring’s oscillations to slow down, but should it also fall to the ground more slowly? 跟全局速度挂钩
        -   无法表示弹簧内部的损耗
-   Internal Damping for Spring
    
   ![[Pasted image 20221008134308.png]]
    -   Viscous drag only on change in spring length
        -   Won’t slow group motion for the spring system (e.g. global translation or rotation of the group)
    -   Note: This is only one specific type of damping 只是一种阻尼的近似

## Structures from Springs

-   Sheets
-   Blocks
-   Others
    -   比如说，一块布的进化

Step 1: Sheets

-   This structure will not resist shearing切变会露馅
-   This structure will not resist out-of-plane bending...

Step 2: 加强筋

-   This structure will resist shearing but has anisotropic bias 各向异性
-   This structure will not resist out-of-plane bending either...

Step 3: 加强筋 plus

-   This structure will resist shearing. Less directional bias.
-   This structure will not resist out-of-plane bending either... 弯折

Step 4: 加强筋 max （skip connection）

-   This structure will resist shearing. Less directional bias.
-   This structure will resist out-of-plane bending （Red springs should be much weaker）
![[Pasted image 20221008134532.png|125]]![[Pasted image 20221008134538.png|125]]![[Pasted image 20221008134547.png|125]]![[Pasted image 20221008134554.png|125]]
## FEM (Finite Element Method) Instead of Springs

有限元方法

-   车辆碰撞

力传导扩散适合用有限元方法建模做

# 动画系统之Particle Systems粒子系统

-   建模定义很多粒子
-   每个粒子有自己的属性

Model dynamical systems as collections of large numbers of particles

Each particle’s motion is defined by a set of physical (or non-physical) forces

Popular technique in graphics and games

• Easy to understand, implement

• Scalable: fewer particles for speed, more for higher complexity

Challenges

• May need many particles (e.g. fluids)

• May need acceleration structures (e.g. to find nearest particles for interactions)

For each frame in animation

• [If needed] Remove dead particles

• Calculate forces on each particle

• Update each particle’s position and velocity

• [If needed] Create new particles

• Render particles

定义个体和群体之间的关系

### Particle System Forces

Attraction and repulsion forces

• Gravity, electromagnetism, …

• Springs, propulsion, …

Damping forces

• Friction, air drag, viscosity, …

Collisions

• Walls, containers, fixed objects, …

• Dynamic objects, character body parts, …

星系模拟、Particle-Based Fluids

Example: Simulated Flocking as an ODE

-   定义鸟儿之间交互的规则：个体对群体的观察
-   Model each bird as a particle Subject to very simple forces:
-   attraction to center of neighbors
-   repulsion from individual neighbors
-   alignment toward average trajectory of neighbors Simulate evolution of large particle system numerically Emergent complex behavior (also seen in fish, bees, ...)

Example: Molecular Dynamics

Example: Crowds + “Rock” Dynamics

# Kinematics

运动学：正向和反向

## Forward Kinematics 正向运动学

明确骨骼之间的运动关系→计算出各个部位的位置

Articulated skeleton

-   Topology (what’s connected to what)
-   Geometric relations from joints
-   Tree structure (in absence of loops)

Joint types

-   Pin (1D rotation)
-   Ball (2D rotation)
-   Prismatic joint (translation)

Strengths

-   Direct control is convenient 无法直接控制
-   Implementation is straightforward

Weaknesses

-   Animation may be inconsistent with physics
-   Time consuming for artists

## Inverse Kinematics 逆运动学

限制各个部位（通常只有终端）的位置、限制骨骼的运动方式→计算骨骼的运动

方便控制形体整体形状

解特别复杂，可能并不唯一

解法：随机化算法（优化方法，梯度下降）

Numerical solution to general N-link IK problem

• Choose an initial configuration

• Define an error metric (e.g. square of distance between goal and current position)

• Compute gradient of error as function of configuration

• Apply gradient descent (or Newton’s method, or other optimization procedure)

例子：Style-Based IK

# Rigging

对形体的控制，像木偶一样

Rigging is a set of higher level controls on a character that allow more rapid & intuitive modification of pose, deformations, expression, etc.

Important

• Like strings on a puppet

• Captures all meaningful character changes

• Varies from character to character

Expensive to create

• Manual effort 定控制点，拉控制点（应该怎么定、应该怎么拉 → 动画师）

• Requires both artistic and technical training

## Blend Shapes 控制点间的位置插值计算

Instead of skeleton, interpolate directly between surfaces

E.g., model a collection of facial expressions:

Simplest scheme: take linear combination of vertex positions

Spline used to control choice of weights over time

# Motion Capture

真人控制点反映到虚拟角色中去，需要建立真实和虚拟的联系

Data-driven approach to creating animation sequences

-   Record real-world performances (e.g. person executing an activity)
-   Extract pose as a function of time from the data collected

Strengths

• Can capture large amounts of real data quickly

• Realism can be high

Weaknesses

• Complex and costly set-ups 复杂、花钱

• Captured animation may not meet artistic needs, requiring alterations 不符合艺术家要求，不可能实现的动作

捕捉条件限制

不同的捕捉方法：

-   Optical (More on following slides)
    -   Markers on subject
    -   Positions by triangulation from multiple cameras
    -   8+ cameras, 240 Hz, occlusions are difficult
-   Magnetic Sense magnetic fields to infer position / orientation. Tethered.
-   Mechanical Measure joint angles directly. Restricts motion.

很花钱

Data可以可视化成一些曲线

![[Pasted image 20221008135007.png]]
Challenges of Facial Animation

-   Uncanny valley
    -   In robotics and graphics
    -   As artificial character appearance approaches human realism, our emotional response goes negative, until it achieves a sufficiently convincing level of realism in expression

Facial Motion Capture

Example：阿凡达