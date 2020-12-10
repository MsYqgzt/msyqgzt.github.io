---
title: Unity 屏幕震动效果
tags: [游戏编程]
categories: 
- [Unity]
- [C#]
mathjax: true
date: 2020-12-10
---



```c#
using UnityEngine;

public class ScreenShake : MonoBehaviour
{
    private float shakeTime = 0.0f;
    private float fps = 20.0f;
    private float frameTime = 0.0f;
    private float shakeDelta = 0.005f;

    #region Inspector
    public Camera cam;
    public static bool isShakeCamera = false;
    #endregion
    
    // Use this for initialization
    void Start()
    {
        shakeTime = 2.0f;
        fps = 20.0f;
        frameTime = 0.03f;
        shakeDelta = 0.005f;
        //isshakeCamera=true;//启动后查看一次效果
    }

    // Update is called once per frame
    void Update()
    {
        if (isShakeCamera)
        {
            if (shakeTime > 0)
            {
                shakeTime -= Time.deltaTime;
                if (shakeTime <= 0)
                {
                    cam.rect = new Rect(0.0f, 0.0f, 1.0f, 1.0f);
                    isShakeCamera = false;
                    shakeTime = 1.0f;
                    fps = 20.0f;
                    frameTime = 0.03f;
                    shakeDelta = 0.005f;
                }
                else
                {
                    frameTime += Time.deltaTime;

                    if (frameTime > 1.0 / fps)
                    {
                        frameTime = 0;
                        cam.rect = new Rect(shakeDelta * (-1.0f + 2.0f * Random.value), shakeDelta * (-1.0f + 2.0f * Random.value), 1.0f, 1.0f);
                    }
                }
            }
        }
    }
}
```

