---
title: 'Unity中的"常规操作"'
tags: [IT,C#]
categories: 
- [编程]
- [游戏]
mathjax: true
---



## 总觉得要屯点什么🌰才开心

### 物体的生成、销毁、物理加力

将代码挂载在需要继承方向等信息的物体上：

```c#
//生成物体函数(物体的变换信息，位置，旋转)
Transform n = Instantiate(new Transform(), transform.position, transform.rotation);
//获取被挂载物体的Z轴正方向
Vector3 fwd = transform.TransformDirection(Vector3.forward);
//给生成的物体n加上力fwd，前提是物体具备刚体组件
n.GetComponent<Rigidbody>().AddForce(fwd * 5000);
```

挂在销毁函数在生成的物体：

```c#
Destroy(gameObject);
```



### 3D游戏中的角色

#### 简单跳跃模块

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



#### 鼠标自由视角

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



#### QE控制环绕视角

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

