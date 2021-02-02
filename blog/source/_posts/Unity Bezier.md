---
title: Unity 贝塞尔(Bezier)曲线绘制
tags: [游戏编程, 算法]
categories: 
- [Unity]
- [C#]
mathjax: true
date: 2021-02-02
---



```c#
using System.Collections.Generic;
using UnityEngine;

[ExecuteInEditMode]
public class BezierCurvePointRenderer : MonoBehaviour
{
    [Header("依赖组件")]
    public LineRenderer lineRenderer;

    [Header("顶点集合")]
    [Tooltip("全部顶点集合，按顺序连接")]
    public Transform[] positions;

    [Header("[可选]构建曲面的顶点")]
    public Transform point1;
    public Transform point2;
    public Transform point3;

    [Header("相关参数")]
    [Tooltip("构建曲线的顶点数，数值越大曲线越平滑")]
    public int vertexCount;

    List<Vector3> pointList;//绘制点集合
    void Start()
    {
        pointList = new List<Vector3>();
    }
    void Update()
    {
        //BezierCurveWithThree();

        BezierCurveWithUnlimitPoints();

        lineRenderer.positionCount = pointList.Count;
        lineRenderer.SetPositions(pointList.ToArray());
    }

    /// <summary> 三点Bezier绘制 </summary>
    void BezierCurveWithThree()
    {
        pointList.Clear();
        for (float ratio = 0; ratio <= 1; ratio += 1.0f / vertexCount)
        {
            Vector3 tangentLineVertex1 = Vector3.Lerp(point1.position, point2.position, ratio);
            Vector3 tangentLineVertex2 = Vector3.Lerp(point2.position, point3.position, ratio);
            Vector3 bezierPoint = Vector3.Lerp(tangentLineVertex1, tangentLineVertex2, ratio);
            pointList.Add(bezierPoint);
        }
        pointList.Add(point3.position);
    }

    /// <summary> 无限点Bezier绘制 </summary>
    public void BezierCurveWithUnlimitPoints()
    {
        pointList.Clear();
        for (float ratio = 0; ratio <= 1; ratio += 1.0f / vertexCount)
        {
            pointList.Add(UnlimitBezierCurve(positions, ratio));
        }
        pointList.Add(positions[positions.Length - 1].position);
    }
    public Vector3 UnlimitBezierCurve(Transform[] trans, float t)
    {
        Vector3[] temp = new Vector3[trans.Length];
        for (int i = 0; i < temp.Length; i++)
        {
            temp[i] = trans[i].position;
        }
        int n = temp.Length - 1;
        for (int i = 0; i < n; i++)
        {
            for (int j = 0; j < n - i; j++)
            {
                temp[j] = Vector3.Lerp(temp[j], temp[j + 1], t);
            }
        }
        return temp[0];
    }

    /// <summary> 绘制函数 </summary>
    void OnDrawGizmos()
    {
        #region 无限制顶点数

        Gizmos.color = Color.green;

        for (int i = 0; i < positions.Length - 1; i++)
        {
            Gizmos.DrawLine(positions[i].position, positions[i + 1].position);
        }

        Gizmos.color = Color.red;

        Vector3[] temp = new Vector3[positions.Length];
        for (int i = 0; i < temp.Length; i++)
        {
            temp[i] = positions[i].position;
        }
        int n = temp.Length - 1;
        for (float ratio = 0.5f / vertexCount; ratio < 1; ratio += 1.0f / vertexCount)
        {
            for (int i = 0; i < n - 2; i++)
            {
                Gizmos.DrawLine(Vector3.Lerp(temp[i], temp[i + 1], ratio), Vector3.Lerp(temp[i + 2], temp[i + 3], ratio));
            }

        }
        #endregion
    }
}
```

