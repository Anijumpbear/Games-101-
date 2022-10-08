# Rasterization 1 (Triangles)
- 视锥
	- aspect ratio = width/height
	- vertical field of view(FOV)
	- 与n 推出l r   t b 
## viewport(视口) transform
![[Pasted image 20220820121108.png]]
## rasterizing triangles into pixels
- sampling(采样) a function
- 光栅化加速
	- bounding box(包围盒)
	- 三角内部每行从左到右(适用 thin and rotated triangles)
## aliasing(jaggies)

# Rasterization 2 (Antialiasing and Z-Buffering)
- antialiasing
	- theory
		- artifacts(erroes/mistakes/inaccuracies) in CG
			eg. 1)jaggies--sampling in space   2)moire patterns(摩尔纹)--images   3)wagon wheel illusion(车轮幻觉 )--time
			采样跟不上变化频率
		- frequency domain(频域)   f = 1/T
			- ![[Pasted image 20220820154357.png]]
			傅里叶变换 f(x)→F(x)把函数变成不同频率段
		- filtering = convolution = averaging(时域卷积=频域乘积)
			- ![[Pasted image 20220820160054.png]]
		- 采样
			- ![[Pasted image 20220820161638.png]]
		- 走样=频域复制混合
			- ![[Pasted image 20220820161954.png]]
	- practice
		-  增加采样率
		-  先模糊(频域low pass或时域平均)再采样
			-  ![[Pasted image 20220820162449.png]]
		- eg.MSAA(multisampling antialiasing)
			-  ![[Pasted image 20220820164311.png]]
		- FXAA(fast approximate antialiasing)
		- TAA(temporal antialiasing)
		- super resolution/sampling 超分辨率
		- DLSS
- visibility/occlusion
	- Z-buffering