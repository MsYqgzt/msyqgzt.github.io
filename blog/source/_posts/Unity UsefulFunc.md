---
title: Unity 重要的函数
tags: [游戏编程]
categories: 
- [Unity]
- [C#]
mathjax: true
date: 2020-12-10
---



|             函数名              |                             描述                             |
| :-----------------------------: | :----------------------------------------------------------: |
|              Awake              |                 当一个脚本实例被载入时被调用                 |
|              Start              |               仅在Update函数第一次被调用前调用               |
|             Update              |        当MonoBehaviour启用时，其Update在每一帧被调用         |
|           LateUpdate            |        当Behaviour启用时，其LateUpdate在每一帧被调用         |
|           FixedUpdate           |          当MonoBehaviour启用时，每固定时间调用一次           |
|              Reset              |                         重置为默认值                         |
|          OnMouseEnter           |  当鼠标进入到GUIElement(GUI元素)或Collider(碰撞体)中时调用   |
|           OnMouseOver           |  当鼠标悬浮在GUIElement(GUI元素)或Collider(碰撞体)上时调用   |
|           OnMouseExit           |   当鼠标移出GUIElement(GUI元素)或Collider(碰撞体)上时调用    |
|           OnMouseDown           |  当鼠标在GUIElement(GUI元素)或Collider(碰撞体)上点击时调用   |
|            OnMouseUp            |                   当用户释放鼠标按钮时调用                   |
|        OnMouseUpAsButton        |   只有当鼠标在同一个GUIElement或Collider按下，在释放时调用   |
|           OnMouseDrag           |  当用户鼠标拖拽GUIElement(GUI元素)或Collider(碰撞体)时调用   |
|         OnTriggerEnter          |         当Collider(碰撞体)进入trigger(触发器)时调用          |
|          OnTriggerExit          |       当Collider(碰撞体)停止触发trigger(触发器)时调用        |
|          OnTriggerStay          |             当碰撞体接触触发器时，在每一帧被调用             |
|        OnCollisionEnter         | 当此Collider/Rigidbody触发另一个Rigidbody/Collider时，OnCollisionEnter将被调用 |
|         OnCollisionExit         | 当此Collider/Rigidbody停止触发另一个Rigidbody/Collider时，OnCollisionExit将被调用 |
|         OnCollisionStay         | 当此Collider/Rigidbody触发另一个Rigidbody/Collider时，OnCollisionStay将会在每一帧被调用。 |
|     OnControllerColliderHit     |        在移动的时，当controller碰撞到Collider时被调用        |
|          OnJointBreak           |              当附在同一对象上的关节被断开时调用              |
|       OnParticleCollision       |                  当粒子碰到Collider时被调用                  |
|         OnBecameVisible         |           当renderer(渲染器)在任何相机上可见时调用           |
|        OnBecameInvisible        |         当renderer(渲染器)在任何相机上都不可见时调用         |
|        OnLevelWasLoaded         |               当一个新关卡被载入时此函数被调用               |
|            OnEnable             |            当对象变为可用或激活状态时此函数被调用            |
|            OnDisable            |          当对象变为不可用或非激活状态时此函数被调用          |
|            OnDestroy            |          当MonoBehaviour将被销毁时，这个函数被调用           |
|            OnPreCull            |                   在相机消隐场景之前被调用                   |
|           OnPreRender           |                   在相机渲染场景之前被调用                   |
|          OnPostRender           |                 在相机完成场景渲染之后被调用                 |
|         OnRenderObject          |                  在相机场景渲染完成后被调用                  |
|       OnWillRenderObject        |                如果对象可见每个相机都会调用它                |
|              OnGUI              |                   渲染和处理GUI事件时调用                    |
|          OnRenderImage          |       当完成所有渲染图片后被调用，用来渲染图片后期效果       |
|      OnDrawGizmosSelected       |        如果你想在物体被选中时绘制gizmos，执行这个函数        |
|          OnDrawGizmos           |          如果你想绘制可被点选的gizmos，执行这个函数          |
|       OnApplicationPause        |               当玩家暂停时发送到所有的游戏物体               |
|       OnApplicationFocus        |           当玩家获得或失去焦点时发送给所有游戏物体           |
|        OnApplicationQuit        |              在应用退出之前发送给所有的游戏物体              |
|        OnPlayerConnected        |            当一个新玩家成功连接时在服务器上被调用            |
|       OnServerInitialized       | 当Network.InitializeServer被调用并完成时，在服务器上调用这个函数 |
|       OnConnectedToServer       |             当你成功连接到服务器时，在客户端调用             |
|      OnPlayerDisconnected       |           当一个玩家从服务器上断开时在服务器端调用           |
|    OnDisconnectedFromServer     |           当失去连接或从服务器端断开时在客户端调用           |
|        OnFailedToConnect        |           当一个连接因为某些原因失败时在客户端调用           |
| OnFailedToConnectToMasterServer |        当报告事件来自主服务器时在客户端或服务器端调用        |
|       OnMasterServerEvent       |        当报告事件来自主服务器时在客户端或服务器端调用        |
|      OnNetworkInstantiate       |    当一个物体使用Network.Instantiate进行网络初始化时调用     |
|     OnSerializeNetworkView      | 当序列化networkView时调用，只有NetworkView的owner才能写入数据流 |
