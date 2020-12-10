---
title: Unity 场景的淡出淡入切换
tags: [游戏编程]
categories: 
- [Unity]
- [C#]
mathjax: true
date: 2020-12-10
---



```c#
using UnityEngine;
using UnityEngine.SceneManagement;

/*
 * Usage：将该脚本赋予摄像机，然后切换场景时镜头不销毁
 * 要调用的时候获得该脚本，调用StartSplash（）;
 * Unity5.0 使用Application.LoadLevel(levelToLoadInt);加载场景
 * Unity5.0 之后版本使用SceneManager.LoadScene(sceneToLoadName);加载场景
 */

public class SceneLoad : MonoBehaviour
{
    public int guiDepth = 0;

    ////将要加载的场景序号
    //public int levelToLoadInt;

    //将要加载的场景名称
    public string sceneToLoadName;

    //切换场景的纹理
    public Texture2D splashLogo;

    //淡入淡出的速度
    float fadeSpeed = 0.8f;

    //保持纹理最高透明度的时间
    float waitTime = 0f;

    //是否要等待输入
    public bool waitForInput = false;

    //public bool startAutomatically = false;

    public bool IsDealPlayer = false;
    float timeFadingInFinished = 0.0f;

    //处理切换场景后需要实现的事件
    public delegate void EventHandler();
    public event EventHandler trigger;
    public delegate void EventHandler2nd();
    public event EventHandler2nd triggerAtLoading;

    //淡入淡出方式
    public enum SplashType
    {
        LoadNextLevelThenFadeOut,
        FadeOutThenLoadNextLevel
    }

    public SplashType splashType;
    float alpha = 0.0f;

    //纹理的状态
    enum FadeStatus
    {
        Paused,
        FadeIn,
        FadeWaiting,
        FadeOut
    }

    FadeStatus status = FadeStatus.Paused;
    Rect splashLogoPos = new Rect();

    //是否要自适应屏幕大小
    public enum LogoPositioning
    {
        Centered,
        Stretched
    }

    public LogoPositioning logoPositioning;
    //bool loadingNextLevel = false;
    void Start()
    {
        if (logoPositioning == LogoPositioning.Centered)
        {
            splashLogoPos.x = (Screen.width * 0.5f) - (splashLogo.width * 0.5f);
            splashLogoPos.y = (Screen.height * 0.5f) - (splashLogo.height * 0.5f);
            splashLogoPos.width = splashLogo.width;
            splashLogoPos.height = splashLogo.height;
        }
        else
        {
            splashLogoPos.x = 0;
            splashLogoPos.y = 0;

            splashLogoPos.width = Screen.width;
            splashLogoPos.height = Screen.height;
        }

        if (splashType == SplashType.LoadNextLevelThenFadeOut)
        {
            DontDestroyOnLoad(this);
        }
        if (SceneManager.sceneCount <= 1)
        {
            Debug.LogWarning("Invalid levelToLoad value.");
        }
    }

    /// <summary> 开始切换场景 </summary>
    /// <param name="scene">要跳转的场景</param>
    /// <param name="i">对应的切换速率</param>
    public void StartSplash(string scene, int i)
    {
        status = FadeStatus.FadeIn;
        //levelToLoadInt = level;
        sceneToLoadName = scene;
        SetValue(i);
    }

    public void setIsDealPlayer(bool isDealPlayer)
    {
        IsDealPlayer = isDealPlayer;
    }

    public void SetValue(int i)
    {
        switch (i)
        {
            case 1:
                fadeSpeed = 1f;
                waitTime = 0.5f;
                break;

            case 2:
                fadeSpeed = 1.5f;
                waitTime = 0.8f;
                break;

            case 3:
                fadeSpeed = 2.5f;
                waitTime = 0.5f;
                break;
        }
    }

    void Update()
    {
        switch (status)
        {
            case FadeStatus.FadeIn:
                alpha += fadeSpeed * Time.deltaTime;
                break;

            case FadeStatus.FadeWaiting:
                if ((!waitForInput && Time.time >= timeFadingInFinished + waitTime) || (waitForInput && Input.anyKey))
                {
                    status = FadeStatus.FadeOut;
                }
                break;

            case FadeStatus.FadeOut:
                alpha += -fadeSpeed * Time.deltaTime * 2;
                if (alpha <= 0.0f)
                {
                    if (trigger != null)
                    {
                        trigger();
                    }
                    status = FadeStatus.Paused;
                }
                break;
        }
    }

    void OnGUI()
    {
        GUI.depth = guiDepth;

        if (splashLogo != null)
        {
            GUI.color = new Color(GUI.color.r, GUI.color.g, GUI.color.b, Mathf.Clamp01(alpha));
            GUI.DrawTexture(splashLogoPos, splashLogo);
        }

        if (alpha > 1.0f)
        {
            status = FadeStatus.FadeWaiting;
            timeFadingInFinished = Time.time;
            alpha = 1.0f;

            if (splashType == SplashType.LoadNextLevelThenFadeOut)
            {
                //loadingNextLevel = true;
                if ((SceneManager.sceneCount) >= 1)
                {
                    //print(levelToLoadInt);
                    SceneManager.LoadScene(sceneToLoadName);
                    //Application.LoadLevel(levelToLoadInt);
                    if (triggerAtLoading != null)
                        triggerAtLoading();
                }
            }
        }

        if (alpha < 0.0f)
        {
            if (splashType == SplashType.FadeOutThenLoadNextLevel)
            {
                if (SceneManager.sceneCount >= 1)
                {
                    SceneManager.LoadScene(sceneToLoadName);
                    //Application.LoadLevel(levelToLoadInt);
                }
            }
            else
            {

            }
        }
    }
}
```

