---
layout: post
title: C# 在初始化時使用靜態成員以及靜態建構子
author: Yu-Qiao Hong
modified: 2016-09-12
tags: [C#, Effective C#]
---　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　

在 C# 中最常見的使用 singleton 方式為：

1. class 建構函式設為 "**private**"，防止其他人 new 產生實體

2. 產生一個唯讀的實體供外使用

以下使用靜態成員初始化來產生實體：

~~~csharp
public class MySingleton
{
    private static readonly MySingleton theOneAndOnly = new MySingleton();

    public static MySingleton TheOnly
    {
        get { return theOneAndOnly; }
    }

    private MySingleton()
    {
    }
}
~~~

----------

若要處理較複雜的邏輯，或為例外的處理，可使用靜態建構子，須注意的是靜態建構子**不同**於一般的 class 建構子，它不能帶入任何的參數

~~~csharp
public class MySingleton2
{
    private static readonly MySingleton2 theOneAndOnly;

    static MySingleton2()
    {
        try
        {
            theOneAndOnly = new MySingleton2();
        }
        catch
        {
            // ...
        }
    }

    public static MySingleton2 TheOnly
    {
        get { return theOneAndOnly; }
    }

    private MySingleton2()
    {
    }
}
~~~

client 端程式：

~~~csharp
static void Main(string[] args)
{
    MySingleton a1 = MySingleton.TheOnly;
    MySingleton a2 = MySingleton.TheOnly;
    if (a1 == a2)
    {
        Console.WriteLine("The same");
    }
    
    MySingleton2 b1 = MySingleton2.TheOnly;
    MySingleton2 b2 = MySingleton2.TheOnly;
    if (b1 == b2)
    {
        Console.WriteLine("The same");
    }
}
~~~

結果：

    The same
    The same