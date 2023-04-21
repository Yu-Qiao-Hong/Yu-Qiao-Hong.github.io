---
layout: post
title: C# Nullable Type
author: Yu-Qiao Hong
modified: 2016-04-12
tags: [C#]
---

## 使用時機

操作資料庫或是定義一資料結構時，使用到 C# 的 value type，例如:

~~~c#
class Test
{
    public int x;
    public int y;
}
~~~

若是從資料庫取不到資料，可能此變數 x 就不去 assign 值，但是在讀取的時候還是讀取的到

~~~c#
Test c = new Test();
Console.WriteLine(c.x.ToString());  // 0
~~~

明明是沒有 assign 值阿，並不是 int 的初始值 0 阿!!
啊!我把 x 初始化給一個 -9999 的值代表沒有被 assign(此寫法很常見)
但這方法很不好，很明顯你會在你後面程式加一段

~~~c#
if(c.x == -9999) // not assignment
{
    //...
}
~~~

若好死不剛好就剛好出現 -9999 那就又頭大了
這個時候就是 nullable type 該出場的時候了。

----------

## Nullable Type

有兩種宣告方式，宣告完後初始值皆為 null
第一種:

~~~c#
Nullable<int> x;
~~~

第二種:

~~~c#
int? x;
~~~

我個人傾向使用第二種方式，簡單明瞭。
後面判斷時可用"**HasValue**" property 得知是否有值

~~~c#
Test c = new Test();
if (c.x.HasValue)
{
    Console.WriteLine(c.x.ToString());
}
else
{
    Console.WriteLine("not assign");
}
~~~

----------

### Nullable type assign to non-nullable type

若現在要把 x 的值 assign 給 y,

~~~c#
y = x; // compiler error
~~~

因為 y 不是nullable type，所以在使用時須強制轉換

~~~c#
y = (int) x;
~~~

這邊需要注意的是，若 x 為 null 的話會產生 exception。