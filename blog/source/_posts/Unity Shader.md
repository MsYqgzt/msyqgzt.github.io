---
title: Unity Shader入门
tags: [游戏编程]
categories: 
- [Unity]
- [图形学]
mathjax: true
date: 2021-02-20
---

## 认识Shader

### Shader的种类

OpenGL的渲染流程：

```mermaid
graph LR
Start1(几何顶点数据) --> 运算器 --> 逐个顶点操作和图元组装 --> 光栅化
Start1(几何顶点数据) & Start2(图像像素数据)--> 显示列表
Start2(图像像素数据) --> 图像操作 --> 纹理映射--> 光栅化
光栅化 --> 逐个图元操作 --> End(帧缓存区)
```

### Shader的开发语言

- **HLSL：**主要用于Direct3D。
  - 平台：windows。

- **GLSL：**主要用于OpenGL。 
  - 平台：移动平台（iOS，安卓），MAC(only use when you target Mac OS X or OpenGL ES 2.0)

- **CG：**与DirectX 9.0以上以及OpenGL 完全兼容。
  - 运行时或事先编译成GPU汇编代码。
  - CG比HLSL、GLSL支持更多的平台，Unity Shader采用CG/HLSL作为开发语言。