---
layout: post
title: C# Anonymous Type
author: Yu-Qiao Hong
modified: 2016-6-30
tags: [C#]
---

## Anonymous Type

有時候在寫程式時，需要一個暫時的類別來儲存資料，但又不想特定去制定一個類別，這時候就可以使用 Anonymous Type。

### 直接使用

~~~csharp
var a = new { Name = "Iverson Hong", Age = 18 };
Console.WriteLine(a.Name); // Iverson Hong
Console.WriteLine(a.Age); // 18
~~~

### 當作參數使用

client 端呼叫:

~~~csharp
TestMethod(new { Name = "Iverson Hong", Age = 18 });
~~~

Anonymous Type 繼承自"**System.Object**"，故 method 參數以 object 為其類別。可使用反射來取得 property 數值:

~~~csharp
void TestMethod(object a)
{
    PropertyInfo[] propertyInfo = a.GetType().GetProperties();

    for(int i = 0; i < propertyInfo.Length; i++)
    {
        Console.WriteLine(propertyInfo[i].GetValue(a));
    }
}
~~~

這邊需要注意的是 Anonymous Type 所有的的 property 都是 **read only**，假設改寫上述 method 並重新 assign 值則會出現 exception

~~~csharp
static void TestMethod(object a)
{
    dynamic b = a;
    b.Age = 20;

    PropertyInfo[] propertyInfo = a.GetType().GetProperties();
    for (int i = 0; i < propertyInfo.Length; i++)
    {
        Console.WriteLine(propertyInfo[i].GetValue(a));
    }
}
~~~

執行到

~~~csharp
b.Age = 20;
~~~

則跳出 exception 視窗

![]({{ "/images/CSharp_Anonymous_Type/001.png" | relative_url}})