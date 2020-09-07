---
title: 'RPG游戏/特效制作-硬核笔记'
tags: [IT,游戏]
categories:
- [游戏] 
- [编程]
mathjax: true
---

## 准备工作
$
准备工作\begin{cases}
角色导入\begin{cases}模型\\\\动画\\\\贴图\end{cases}\\\\
UI\begin{cases}血量\\\\法力值\\\\界面\\\\…\end{cases}\\\\
AI(Bot)\begin{cases}扣血\\\\对象\\\\移动\end{cases}
\end{cases}
$

## 处理素材文件
- 从Maya导出素材
	- 修改世界坐标为Z轴朝上(UE4可忽略)
		- `window`-`Preferences`-`settings`-`Word Coordinate System`
	- 显示骨骼朝向，确保x轴为骨骼方向
	- 增加武器插槽节点(fire position)在根骨骼下
		- 该节点与根骨骼不能用来k动画
	- 导出FBX文件
		-  默认模型
		- 模型动画（勾选`Animation-Bake`动画）
	- 使用`Marmoset Toolbag`测试渲染模型
		- 导入材质
		- 导入动画
- 素材导入UE4
	- `Character`导入默认角色模型
		- `Import Mesh/Skeletal`
	- 创建材质
		- 导入贴图
		- 新建材质球
		- 贴图拖入材质球
			- 颜色贴图->`Base Color`
			- 法线贴图->`Normal`
			- 高光贴图->`Specular`
		- 材质球拖入模型
		- 创建质感
			- 金属质感：数字键1+左键单击->`Metallic`
			- 反光质感：数字键1+左键单击->`Roughness`
	- 导入动画
		- 去勾选Mesh
		- `Skeleton`选择骨骼
	
	- 替换角色模型
	  - 资源管理面板->`Blueprints`->`TopDownCharacter`
	  - 选中`Mesh`->返回选择要使用的模型->回到面板，右侧`Mesh`导入
	  - 覆盖其他动作模型（同命名），骨骼同理
	  - 修改动画蓝图
	    - 修改`TopDownIdleRun`骨骼绑定
	    - 根据模型状态，拖拽动画文件至动画混合的速度轴
	    - 修改`TopDownAnimBlueprint`骨骼绑定

## 血量材质

> - 文件结构
>   - UI
>     - MAT（材质）
>       - blood
>     - Texture（贴图）
>       - 血量黑白纹理
>       - 球形显示黑白形状

- 导入血量材质
  - Mat文件夹中新建材质Blood
  - 选中贴图，切换到材质编辑器
  - 按住T，左键单击导入
- 定义颜色
  - 按住3，创建RGB属性
  - M键+单击创建混合属性，连接颜色和材质
  - Blend Mode设置为Masked，Shading Mode设为Unlit，以将黑色部分设置为透明 ->`Emissive Color`（高光层）
- 设置缩放：`TextureCoordinate`属性
- 动态化：按住P键单击放置`Panner`，设置贴图速度
- 复制多层参数不同的贴图，按住A点击放置叠加属性，将效果叠加
- 创建底色，叠加效果
- 圆形贴图 ->`Opacity Mask`（透明通道）
- 实现血量反馈
  - 按住0单击放置数值变量
  - 创建`LinearGradient`（线性渐变）
  - 创建if判断
- 创建血量边界
  - 创建`Add`节点
  - 血量数值 -> `Add`
  - 创建血量边界数值 ->`Add`
  - 创建if判断
  - `Add`->`if-A`
  - `LinearGradient`->`if-B`
  - 将if的结果做反向处理，与原先的血量混合->`Multiply`
  - 设置单独的`Panner`和`TextureCoordinate`，和血量模块相加
  - 提高亮度

## UI制作

> - 文件结构
>   - UI
>     - Mesh
>     - MAT
>     - Texture
>       - UI框架贴图

- Mesh导入贴图，MAT创建材质
- 创建显示组件（Cube），导入材质，移动到摄像机前合适位置
- 同理创建血量球材质
- 绑定UI和摄像机
  - 在事件蓝图下创建`Event Begin Play`
  - 选中UI框架，创建`Attach To(UI框架)`
  - 将摄像机拖拽到蓝图内
  - 绑定位置
    - `Event Begin Play`->`Attach To(UI框架)`根节点
    - `Attach To(UI框架)`->`Attach To(血量球)`根节点
    - Camera ->`In Parent`
    - `Attach Type`=`Keep World Position`

- 动态材质获取
  - 编辑角色蓝图，新建`Health`变量(`float`)
  - 新建`血量`变量(`MaterialInstanceDynamic`)
  - 将血槽材质与血量绑定
    - 选中血槽组件
    - `Construction Script`->`Create Dynamic Material Instance`(血槽) -`Return Value`->SET（血量变量）
  - 编辑事件蓝图，新建`Event Tick`-> 新建`Set Scalar Parameter Value`
    - 血量变量->`Target`
    - `Health`->`Value`
    - 回到血量材质编辑蓝图，命名血量数值组件
    - 回到事件蓝图，设置`Parameter Name`
  - 动态改变血量
    - 各种碰撞事件起点->`Cast To （角色名）`->`SET`
      - `As (角色名)`->`Target`
      - `As (角色名)`->`Target  Health`->数值组件(+-*/)->`Health`