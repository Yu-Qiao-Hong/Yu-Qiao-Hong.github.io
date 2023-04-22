---
layout: post
title: C# Property vs. Data Member
author: Yu-Qiao Hong
modified: 2016-07-03
tags: [C#, Effective C#]
---

存取資料時，應盡量使用屬性(property)而不是直接使用資料成員(Data Member)，這樣有著許多好處，以下舉例說明：

## 封裝性 ##

當有一個資料成員如下：

~~~csharp
private int age;
~~~

client 端怎麼進行操作?兩個辦法

1. 把 private 改成 public

2. 寫兩個 function，一個 Set,一個 Get

第一個方法破壞了程式原有的封裝性，第二個方法類似於 C++ 寫法，一旦資料成員一多，對應 function 也需跟著增加。

直接用屬性來進行操作，不但保留了原本的封裝性，也比寫對應的 Get/Set function 來的簡潔。

~~~csharp
private int _age;

public int Age 
{
    get 
    {
        return _age;
    }
    set 
    { 
        _age = value;
    }
}
~~~

----------

## 邏輯判斷 ##

延續上面的例子，假設輸入的年齡不能為負數，可在 property 內增加邏輯判斷，要是直接對資料成員操作的話，這個邏輯判斷會散落在程式的各個地方，一不小心還有可能漏掉。

~~~csharp
private int _age;

public int Age 
{
    get 
    {
        return _age;
    }
    set 
    {
        if (value < 0)
            throw new ArgumentException("Age cannot small than 0");

        _age = value;
    }
}
~~~

----------

## multi-thread ##

延續上面例子，假設是在多執行續的操作下，也可很容易的在 property 內加上 **lock**，而不需在程式各個使用資料成員的地方加上此判斷。

~~~csharp
private int _age;
private object _syncHandle = new object();

public int Age 
{
    get 
    {
        lock (_syncHandle)
        {
            return _age;
        }
    }
    set 
    {
        if (value < 0)
            throw new ArgumentException("Age cannot small than 0");

        lock (_syncHandle)
        {
            _age = value;
        }
    }
}
~~~

## 存取權限 ##

可以簡單的設定存取權限，例如不允許設定值，只可讀取

~~~csharp
public string Name { get; private set; }
~~~