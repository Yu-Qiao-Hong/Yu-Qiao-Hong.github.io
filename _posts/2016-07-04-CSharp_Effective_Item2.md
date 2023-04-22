---
layout: post
title: C# 常數 readonly vs. const
author: Yu-Qiao Hong
modified: 2016-07-04
tags: [C#, Effective C#]
---

在C#中設定常數時，盡量使用 "**readonly**" 而不要使用 "**const**"，這樣有著許多好處，以下舉例說明：

## 行為

const 為c ompile-time 常數；而 readonly 為 runtime 常數。runtime 常數在執行時取值，可以用反組譯(IL)發現 readonly 是參考變數而非其值

測試程式：

~~~csharp
const int myConst = 100;
static readonly int myReadOnly = 200;

private void button1_Click(object sender, EventArgs e)
{
    int test = 150;
    if (test == myConst) ;
    if (test == myReadOnly) ;    
}
~~~

反組譯後程式：

~~~csharp
private const int myConst = 100;
private static readonly int myReadOnly = 200;

private void button1_Click(object sender, EventArgs e)
{
    int test = 150;
    if (test == 100)
    {
    }
    if (test == Form1.myReadOnly)
    {
    }
}
~~~

可以發現使用 const 的變數 myConst 在 if 判斷式中直接以 100 顯示；而 myReadOnly 則還是變數。

再舉個例子：

~~~csharp
class Test
{
    public static readonly int StartValue = 5;
    public const int EndValue = 10;
}
~~~

在另一專案中使用中使用 Test class

~~~csharp
private void button1_Click(object sender, EventArgs e)
{
    for (int i = Test.StartValue; i < Test.EndValue; i++)
    {
        Console.WriteLine("value is {0}", i);
    }
}
~~~

可得到以下結果：

    value is 5
    value is 6
    value is 7
    value is 8
    value is 9

接著更改 Test 內容，並 **沒有** 全部rebuild

~~~csharp
class Test
{
    public static readonly int StartValue = 15;
    public const int EndValue = 20;
}
~~~

預期結果為：

    value is 15
    value is 16
    value is 17
    value is 18
    value is 19

但實際上卻什麼都不會印出，原因是因為專案用到的 EndValue 變數已經存在，其值為 10，而 StartValue 變數是 runtime 也就是15。

----------

## 支援變數型態

const 只能使用 C# 原生的資料型態；而 readonly 則毫無限制。

----------

## 效能

因 const 為 compile-time 故較 readonly 的 runtime 稍快。若非 100% 之後一定不會做更改才建議使用 const(例如圓周率 pi)。