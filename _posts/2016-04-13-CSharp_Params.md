---
layout: post
title: C# Params
author: Yu-Qiao Hong
modified: 2016-04-13
tags: [C#]
---

## 使用時機

有一 method 需帶入 int 陣列，並會將其陣列結果印出:

~~~c#
void PrintNum(int[] ary)
{
    foreach (int n in ary)
        Console.WriteLine(n.ToString());
}
~~~

Clint 在呼叫時需寫:

~~~c#
int[] myAry = {1, 2, 3, 4, 5};
PrintNum(myAry);
~~~

每次 client 要呼叫 method 之前，都先需定義一 int 陣列

~~~c#
int[] myAry2 = {7, 8, 9};
PrintNum(myAry2);
~~~

每次都要重新定義好麻煩喔~~
沒關係!還有別的方法

----------

## Params

修改一下上面的 method，再帶入參數前面加上"**params**"

~~~c#
void PrintNum(params int[] ary)
{
    foreach (int n in ary)
    {
        Console.WriteLine(n.ToString());
    }
}
~~~

這個時候 client 要呼叫時可直接把多個 int 參數帶入

~~~c#
PrintNum(7, 8, 9);
~~~

一樣可以正常印出，且原來的呼叫方式也不會有任何錯誤發生。

----------

## Params 與其他參數混著用

若是要再多帶參數，必須把 params 放在**最後面**，這樣 compiler 才知道 params 陣列的數量到底為多少。

~~~c#
void PrintNum(string name, params int[] ary)
{
    Console.WriteLine(name);
    foreach (int n in ary)
    {
        Console.WriteLine(n.ToString());
    }
}
~~~

~~~c#
int[] myAry = { 1, 2, 3, 4, 5 };
PrintNum("Iverson", myAry);

PrintNum("Hong", 7, 8, 9);
~~~