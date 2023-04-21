---
layout: post
title: C# Optional Parameters
author: Yu-Qiao Hong
modified: 2016-04-14
tags: [C#]
---

## 使用時機

有一 method 須帶入 5 個 int 參數，代表一個 5 位數:

~~~csharp
void test(int a, int b, int c, int d, int e)
{
    int total = a * 10000 + b * 1000 + c * 100 + d * 10 + e;
    Console.WriteLine(total);
}
~~~

Clint 在呼叫時需寫:

~~~csharp
test(1, 2, 3, 4, 5) // 12345
~~~

假設想要顯示出 1000

~~~csharp
test(0, 1, 0, 0, 0) // 1000
~~~

要輸入一堆 0 好麻煩喔~~

一不小心又會輸入錯位數!

沒關係!還有別的方法

----------

## Optional Parameters

修改一下上面的 method，把每個參數加上 default value 0

~~~csharp
void test(int a = 0, int b = 0, int c = 0, int d = 0, int e = 0)
{
    int total = a * 10000 + b * 1000 + c * 100 + d * 10 + e;
    Console.WriteLine(total);
}
~~~

這個時候client要使用時只要直接指定要對哪個參數給值就可以了

~~~csharp
test(b : 1) // 1000
~~~

其輸出結果一樣，此方法在 method 有許多預設參數時非常實用。