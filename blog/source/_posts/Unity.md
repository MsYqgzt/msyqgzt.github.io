---
title: Unity中的"常规操作"
tags: [游戏编程,游戏引擎,C#]
categories: 
- [Unity]
- [游戏]
mathjax: true
date: 2020-10-03
---

## 总觉得要屯点什么🌰才开心

### 逆透视变换

> 将鼠标的屏幕二维坐标转换成三维投射坐标

```c#
public bool unproject_mouse_position(out Vector3 word_position, Vector3 mouse_position)
{
	bool ret;
	float depth;
	
	//用于检测和地面相交的平面
	Plane plane = new Plane(Vector3.up, Vector3.zero);
	
	//穿过摄像机位置和鼠标光标位置的直线
	Ray ray = this.main_camera.GetComponent<Camera>().ScreenPosintToRay(mouse_position);
	
	//求出直线和地面的交点
	if(plane.Raycast(ray,out depth))
	{
		//根据Raycast方法的结果计算交点的坐标
		word_position = ray.origin + ray.direction * depth;
		ret = true;
	}
	else
	{
		world_position = Vector3.zero;
		ret = false;
	}
	
	return(ret);
}
```

## 学以致用

### UI控制摇杆

> **UI的层级结构（对象名 - [类型]描述）：**
>
> - `Canvas` - [Canvas]UI根对象
>   - `Joystick` - [Image]摇杆的可触控范围
>     - `Background` - [Image]摇杆背景
>       - `Handle` - [Image]摇杆对象
>     - `DirectionArrow` - [Image]背景外围的方向箭头

- `EnumFlagsAttribute.cs`

```c#
using UnityEngine;
public class EnumFlagsAttribute : PropertyAttribute { }
```

- `Joystick.cs`

```c#
using UnityEngine;
using UnityEngine.EventSystems;
using UnityEngine.Events;
using UnityEngine.UI;

public class Joystick : MonoBehaviour, IPointerDownHandler, IDragHandler, IPointerUpHandler
{
    public float maxRadius = 100; //Handle 移动最大半径
    [EnumFlags]
    public Direction activatedAxis = (Direction)(-1); //选择激活的轴向
    [SerializeField] bool dynamic = true; // 动态摇杆
    [SerializeField] Transform handle; //摇杆
    [SerializeField] Transform backGround; //背景
    public JoystickEvent OnValueChanged = new JoystickEvent(); //事件 ： 摇杆被 拖拽时
    public JoystickEvent OnPointerDown = new JoystickEvent(); // 事件： 摇杆被按下时
    public JoystickEvent OnPointerUp = new JoystickEvent(); //事件 ： 摇杆上抬起时
    public bool IsDraging { get { return fingerId != int.MinValue; } } //摇杆拖拽状态
    public bool DynamicJoystick //运行时代码配置摇杆是否为动态摇杆
    {
        set
        {
            if (dynamic != value)
            {
                dynamic = value;
                ConfigJoystick();
            }
        }
        get
        {
            return dynamic;
        }
    }
    #region MonoBehaviour functions
    private void Awake() => backGroundOriginLocalPostion = backGround.localPosition;
    void Update() => OnValueChanged.Invoke(handle.localPosition / maxRadius);
    void OnDisable() => RestJoystick(); //意外被 Disable 各单位需要被重置
    #endregion

    #region The implement of pointer event Interface
    void IPointerDownHandler.OnPointerDown(PointerEventData eventData)
    {
        if (eventData.pointerId < -1 || IsDraging) return; //适配 Touch：只响应一个Touch；适配鼠标：只响应左键
        fingerId = eventData.pointerId;
        pointerDownPosition = eventData.position;
        if (dynamic)
        {
            pointerDownPosition[2] = eventData.pressEventCamera?.WorldToScreenPoint(backGround.position).z ?? backGround.position.z;
            backGround.position = eventData.pressEventCamera?.ScreenToWorldPoint(pointerDownPosition) ?? pointerDownPosition; ;
        }
        OnPointerDown.Invoke(eventData.position);
    }

    void IDragHandler.OnDrag(PointerEventData eventData)
    {
        if (fingerId != eventData.pointerId) return;
        Vector2 direction = eventData.position - (Vector2)pointerDownPosition; //得到BackGround 指向 Handle 的向量
        float radius = Mathf.Clamp(Vector3.Magnitude(direction), 0, maxRadius); //获取并锁定向量的长度 以控制 Handle 半径
        Vector2 localPosition = new Vector2()
        {
            x = (0 != (activatedAxis & Direction.Horizontal)) ? (direction.normalized * radius).x : 0, //确认是否激活水平轴向
            y = (0 != (activatedAxis & Direction.Vertical)) ? (direction.normalized * radius).y : 0       //确认是否激活垂直轴向，激活就搞事情
        };
        handle.localPosition = localPosition;      //更新 Handle 位置
    }

    void IPointerUpHandler.OnPointerUp(PointerEventData eventData)
    {
        if (fingerId != eventData.pointerId) return;//正确的手指抬起时才会重置摇杆；
        RestJoystick();
        OnPointerUp.Invoke(eventData.position);
    }
    #endregion

    #region Assistant functions / fields / structures
    void RestJoystick()
    {
        backGround.localPosition = backGroundOriginLocalPostion;
        handle.localPosition = Vector3.zero;
        fingerId = int.MinValue;
    }

    void ConfigJoystick() //配置动态/静态摇杆
    {
        if (!dynamic) backGroundOriginLocalPostion = backGround.localPosition;
        GetComponent<Image>().raycastTarget = dynamic;
        handle.GetComponent<Image>().raycastTarget = !dynamic;
    }

#if UNITY_EDITOR
    private void OnValidate()
    {
        if (!handle) handle = transform.Find("BackGround/Handle");
        if (!backGround) backGround = transform.Find("BackGround");
        ConfigJoystick();
    }
#endif
    private Vector3 backGroundOriginLocalPostion, pointerDownPosition;
    private int fingerId = int.MinValue; //当前触发摇杆的 pointerId ，预设一个永远无法企及的值
    [System.Serializable] public class JoystickEvent : UnityEvent<Vector2> { }
    [System.Flags]
    public enum Direction
    {
        Horizontal = 1 << 0,
        Vertical = 1 << 1
    }
    #endregion
}
```

- `DirectionArrow.cs`

```c#
using System;
using UnityEngine;

public class DirectionArrow : MonoBehaviour
{
    private Joystick joystick;
    private void Start()
    {
        joystick = GetComponentInParent<Joystick>();
        if (null == joystick)
        {
            throw new InvalidOperationException("The directional arrow is an optional part of the joystick and it relies on the instance of the joystick!");
        }
        joystick.OnPointerUp.AddListener(OnPointerUp);
        joystick.OnValueChanged.AddListener(UpdateDirectionArrow);
        gameObject.SetActive(false);
    }

    void OnDestroy()
    {
        joystick.OnPointerUp.RemoveListener(OnPointerUp);
        joystick.OnValueChanged.RemoveListener(UpdateDirectionArrow);
    }
    // 更新指向器的朝向
    private void UpdateDirectionArrow(Vector2 position)
    {
        if (position.magnitude != 0)
        {
            if (!gameObject.activeSelf)
            {
                gameObject.SetActive(true);
            }
            transform.localEulerAngles = new Vector3(0, 0, Vector2.Angle(Vector2.right, position) * (position.y > 0 ? 1 : -1));
        }
    }
    void OnPointerUp(Vector2 pos)
    {
        gameObject.SetActive(false);
    }
}
```



### 简单跳跃模块

- `Player.cs`

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Player : MonoBehaviour
{
    public float jumpHeight = 2f;
    public bool is_landing = false;//着陆标记

    // Start is called before the first frame update
    void Start()
    {
        is_landing = false;
    }

    // Update is called once per frame
    void Update()
    {
        if (is_landing)//着陆后触发
        {
            if (Input.GetKeyDown(KeyCode.Space))
            {
                is_landing = false;
                float jump_speed = Mathf.Sqrt(2f * Mathf.Abs(Physics.gravity.y) * jumpHeight);//v=√2gh
                GetComponent<Rigidbody>().velocity = Vector3.up * jump_speed;
            }
        }
    }

    private void OnCollisionEnter(Collision collision)
    {
        //着陆判定
        if (collision.gameObject.tag == "Floor")
            is_landing = true;
    }
}
```



### 鼠标控制以物体为中心的自由视角

- `freeView.cs`

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class freeView : MonoBehaviour
{
    //摄像机参照的模型
    public Transform target;
    //摄像机距离模型的默认距离
    public float distance = 10.0f;
    //鼠标在x轴和y轴方向移动的速度
    float x;
    float y;
    //限制旋转角度的最小值与最大值
    float yMinLimit = -20.0f;
    float yMaxLimit = 80.0f;
    //鼠标在x和y轴方向移动的速度
    float xSpeed = 250.0f;
    float ySpeed = 120.0f;
    // Use this for initialization

    // Start is called before the first frame update
    void Start()
    {
        //初始化x和y轴角度，使其等于参照模型的角度
        Vector2 Angles = transform.eulerAngles;
        x = Angles.y;
        y = Angles.x;

        if (gameObject.GetComponent<Rigidbody>() != null)
        {
            gameObject.GetComponent<Rigidbody>().freezeRotation = true;
        }
        //Debug.Log (this.transform == this.gameObject.transform);

    }

    // Update is called once per frame
    void Update()
    {

    }
    
    void LateUpdate()
    {
        if (target)
        {
            //根据垂直方向的增减量修改摄像机距离参照物的距离
            distance += Input.GetAxis("Mouse ScrollWheel") * -2f;

            //根据鼠标移动修改摄像机的角度
            x += Input.GetAxis("Mouse X") * xSpeed * 0.02f;
            y -= Input.GetAxis("Mouse Y") * ySpeed * 0.02f;
            //限制了摄像机在X轴方向上的视觉范围
            y = ClampAngle(y, yMinLimit, yMaxLimit);

            Quaternion rotation = Quaternion.Euler(y, x, 0);

            Vector3 position = rotation * new Vector3(0.0f, 0.0f, -distance) + target.position;
            //Debug.Log(position.ToString());

            //设置摄像机的位置与旋转
            transform.rotation = rotation;
            transform.position = position;
        }
    }

    float ClampAngle(float angle, float min, float max)
    {
        //这里之所以要加360，因为比如-380,与-20是等价的
        if (angle < -360)
        {
            angle += 360;
        }
        if (angle > 360)
        {
            angle -= 360;
        }
        return Mathf.Clamp(angle, min, max);
    }
}
```



### QE键控制以物体为中心的环绕视角

- `QERotation.cs`

```c#
using UnityEngine;

public class QERotation : MonoBehaviour
{
    //摄像机参照的模型
    public GameObject target;
    //摄像机距离模型的默认距离
    public float distance = 10.0f;
    //鼠标在x轴和y轴方向移动的速度
    float RotX;
    float RotY;
    //鼠标在x和y轴方向移动的速度
    float xSpeed = 0f;
    //旋转速度
    public float rotValue = 60f;

    // Start is called before the first frame update
    void Start()
    {
        //初始化x和y轴角度，使其等于参照模型的角度
        Vector2 Angles = transform.eulerAngles;
        RotX = Angles.y;

        if (gameObject.GetComponent<Rigidbody>() != null)
        {
            gameObject.GetComponent<Rigidbody>().freezeRotation = true;
        }
        //Debug.Log (this.transform == this.gameObject.transform);
    }

    void LateUpdate()
    {
        if (target)
        {
            //根据垂直方向的增减量修改摄像机距离参照物的距离
            distance += Input.GetAxis("Mouse ScrollWheel") * -2f;

            //根据鼠标移动修改摄像机的角度
            RotX += xSpeed * Time.deltaTime;
            RotY = 30;

            Quaternion rotation = Quaternion.Euler(RotY, RotX, 0);

            //根据模型碰撞箱高度 获取偏移量
            float Offset = target.GetComponent<Collider>().bounds.size.y / 2;

            Vector3 position = rotation * new Vector3(0.0f, Offset, -distance) + target.transform.position;
            //Debug.Log(position.ToString());

            //设置摄像机的位置与旋转
            transform.rotation = rotation;
            transform.position = position;
        }
    }

    // Update is called once per frame
    void Update()
    {
        if (Input.GetKey(KeyCode.Q))
        {
            xSpeed = -rotValue;
        }
        else if (Input.GetKey(KeyCode.E))
        {
            xSpeed = rotValue;
        }
        else
        {
            xSpeed = 0;
        }
    }
}
```

### 查找最近的敌人

```c#
//获取当前物体与敌人的距离
float distance =Vector3.Distance("物体1的位置", "物体2的位置");
```



### 查找hp最小的敌人

- `Enemy.cs`

```c#
using UnityEngine;
using System.Collections;

public class Enemy : MonoBehaviour {
    public float HP;
}
```



- `FindEnemyDemo.cs`

```c#
using UnityEngine;
using System.Collections;

public class FindEnemyDemo : MonoBehaviour {
    //思路：
    //得到所有的敌人获取他们的hp
    //根据当前组件查找其他组件

    //调用
    private void OnGUI()
    {
        if (GUILayout.Button("获取血量最低的敌人"))
        {
            //查找场景中所有有Enemy的对象
            Enemy[] AllEnemy= Object.FindObjectsOfType<Enemy>();
            //获取血量最低的对象
            Enemy min = FindEnemyByMinHP(AllEnemy);
            print(min);
            //根据Enemy类型的引用 获取 其他组件类型的引用
            min.GetComponent<MeshRenderer>().material.color = Color.red;
        }
    }

    //查找血量最低的敌人
    public Enemy FindEnemyByMinHP(Enemy[] allEnemy) 
    {
        //假设 第一个敌人的血最少
        Enemy min = allEnemy[0];
        //第一个依次和兄弟元素进行比较，小于min的就替换掉
        for (int i = 0; i < allEnemy.Length-1; i++)//两个比一次，
        {
            if (min.HP > allEnemy[i+1].HP)
            {
                min=allEnemy[i+1];
            }
        }

        return min;
    }
}
```

