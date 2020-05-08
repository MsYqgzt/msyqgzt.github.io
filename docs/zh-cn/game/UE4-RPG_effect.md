# RPG游戏/特效制作

## 准备工作
$
准备工作\begin{cases}
角色导入\begin{cases}模型\\动画\\贴图\end{cases}\\
UI\begin{cases}血量\\法力值\\界面\\…\end{cases}\\
AI(Bot)\begin{cases}扣血\\对象\\移动\end{cases}
\end{cases}
$

## 处理素材文件
- 从maya导出素材
	- 修改世界坐标为Z轴朝上(UE4可忽略)
		- window-Preferences-settings-Word Coordinate System
	- 显示骨骼朝向，确保x轴为骨骼方向
	- 增加武器插槽节点(fire position)在根骨骼下
		- 该节点与根骨骼不能用来k动画
	- 导出FBX文件
		-  默认模型
		- 模型动画（勾选Animation-Bake动画）
	- 使用Marmoset Toolbag测试渲染模型
		- 导入材质
		- 导入动画
- 素材导入UE4
	- Character导入默认角色模型
		- Import Mesh/Skeletal
	- 创建材质
		- 导入贴图
		- 新建材质球
		- 贴图拖入材质球
			- 颜色贴图->Base Color
			- 法线贴图->Normal
			- 高光贴图->Specular
		- 材质球拖入模型
		- 创建质感
			- 金属质感：数字键1+左键单击->Metallic
			- 反光质感：数字键1+左键单击->Roughness
	- 导入动画
		- 去勾选Mesh
		- Skeleton选择骨骼
	
		
