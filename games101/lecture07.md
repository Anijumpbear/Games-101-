# Z-buffering
- paint算法面对相互遮挡三角形无法排序
- z-buffering(对像素排序) 
	- frame buffer 存点的颜色值
	- 深度图/深度缓存(depth buffer)
		- 只存点的相对位置关系(0~255)
		- 为简化计算与camera相反, 深度测试z值近小远大
		- 遍历光栅化后的三角形,储存更新某一点的z  <使用采样点,更新屏幕像素点
		- 不排序 O(n) → gpu并行  
# Shading 1
- IIIumination & Shaing
- Blinn-Phong reflectance model
		- light→shading point→camera
		 ![[Pasted image 20220920203909.png]]
		- (1)Diffuse Reflection 漫反射
	 ![[Pasted image 20220920212328.png]]
