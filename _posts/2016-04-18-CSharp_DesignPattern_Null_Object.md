---
layout: post
title: C# Null Object Pattern
author: Yu-Qiao Hong
modified: 2016-04-18
tags: [C#, Design Pattern]
---

## 目的/使用時機

* 在 method 中可能回傳 null，並在使用時加上是否為 null 的判斷，想改善這種寫法。

* 實做的內容為 do nothing。

----------

## 說明

簡單來講就是產生一個 object，取代單純回傳 null，其內容不做任何事情(或是做一些 default 的行為)，讓 client 端呼叫時不會發生錯誤。

----------

## 範例

### 建立一個 interface 或 abstract class

~~~csharp
interface ICar
{
    void Run();
}
~~~

### 接著定幾個class繼承上述的介面:

~~~csharp
class FastCar : ICar
{
    public void Run()
    {
        Console.WriteLine("Run fast");
    }
}
~~~

~~~csharp
class SlowCar : ICar
{
    public void Run()
    {
        Console.WriteLine("Run slow");
    }
}
~~~

### 寫個 factory 用來產生實體:

~~~csharp
ICar CarFactory(int i)
{
    if (i == 0)
        return new FastCar();
    else if (i == 1)
        return new SlowCar();
    else
        return null;
}
~~~

### client端程式:

~~~csharp
ICar MyCar = CarFactory(0);
MyCar.Run(); // Run fast
MyCar = CarFactory(1);
MyCar.Run(); // Run slow
MyCar = CarFactory(2);
MyCar.Run();
~~~

執行到最後一個 MyCar.Run() 時，發生**Exception:System.NullReferenceException**

原因為最後一次的 CarFactory 並沒有產生實體，所以在呼叫 Run() 時會出現 exception，那要怎麼解決呢?這時候就可以使用 Null Object Pattern。

----------

### 建立一個null class

一樣繼承 ICar interface， Run() 裡面不做任何事。

~~~csharp
class NullCar: ICar
{
    public void Run()
    {
        // Do nothing
    }
}
~~~

### 更改原來的factory

將原來的 return null 改寫成**return new NullCar()**

新的 factory 變成:

~~~csharp
ICar CarFactory(int i)
{
    if (i == 0)
        return new FastCar();
    else if (i == 1)
        return new SlowCar();
    else
        return new NullCar();
}
~~~

相同的client端程式再呼叫一次則可正常運作，不會有任何錯誤出現。

----------

## 優點

* 減少程式碼中的判斷和條件分支，使程式碼更加簡潔。

* 增加程式的可靠性和可擴展性，因 null 物件較易產生錯誤或例外，而 Null Object 會實作相同的介面或類別，為使用者提供一個預期的、可預測的行為。

----------

## 缺點

* 增加程式的複雜度，因為必須實作一個特殊的 Null Object，並且在程式中使用它。