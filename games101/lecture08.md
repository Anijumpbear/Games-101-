# Shading 2
- BP reflectance model
	- (2) Specular Term 高光
		- ![[Pasted image 20220920214516.png]]
		- ![[Pasted image 20220920214739.png]]
	- (3) Ambient Term 简化环境光,保证不黑
		- ![[Pasted image 20220920215229.png]]
	- 步骤
		- ![[Pasted image 20220920215311.png]]
- shading frequencies 着色频率
	- flat shading 对三角着色
	- gouraud shading 对顶点着色 中间线性插值
	- phong shading 用每个插值出的点的法线对像素着色
	- 对比
		- ![[Pasted image 20220920221334.png]]
	- definding per-vertex normal vectors 逐顶点法线
		- ![[Pasted image 20220920221737.png]]
	- definding per-pixel normal vectors 逐像素法线
		- ![[Pasted image 20220920222005.png]]
# Graphics (Real-time Rendering) Pipeline
![[Pasted image 20220929164358.png]]
-   Vertex Processing
    -   Model, View, Projection transforms
    -   Shading, Texture mapping
    -   Output: Vertex Stream
-   Triangle Processing
    -   Output: Triangle Stream
-   Rasterization
    -   Sampling
    -   Output: Fragment Stream
-   Fragment Processing
    -   Z-Buffer Visibility Tests
    -   Shading, Texture mapping
    -   Output: Shaded Fragments
-   Framebuffer Operations
    -   Output: image (pixels)

Shader Programs

-   Program vertex and fragment processing stages
-   自己编程顶点和像素的着色流程
-   Describe operation on **a single vertex (or fragment)**
-   每个元素都执行一次
-   vertex / fragment shader
-   More:
    -   Geometry Shader
    -   Compute Shader

学习API（OpenGL, DirectX, vulkan）

推荐：ShaderToy，只需要关注着色

当下的实时渲染：

-   100’s of thousands to millions of triangles in a scene
-   Complex vertex and fragment shader computations
-   High resolution (2-4 megapixel + supersampling)
-   30-60 frames per second (even higher for VR)

Graphics Pipeline Implementation: GPUs

-   Specialized processors for executing graphics pipeline computations
-   Heterogeneous, Multi-Core Processor
-   可并行的