---
layout: post
title: C# 使用 as, is operator 進行類型轉換
author: Yu-Qiao Hong
modified: 2016-07-10
tags: [C#, Effective C#]
---

寫程式時有時必須轉換類型進行操作，以下為 C# 類型轉換相關介紹：

## as operator

1. 轉換時不會產生 exception，若無法轉換則回傳 null，使用後應判斷是否等於 null 的來檢查是否成功轉換。

2. 只能轉換成 reference type class，原因為若轉換失敗回傳 null，但 value type 不能為 null，故 compiler 不會過。

範例 1:

~~~csharp
private void button1_Click(object sender, EventArgs e)
{
    object s1 = "Iverson Hong";
    string s2 = s1 as string;
    if (s2 != null)
    {
        Console.WriteLine(s2);
    }
    else
    {
        Console.WriteLine("Incompatible cast");
    }

    StringBuilder s3 = s1 as StringBuilder;
    if (s3 != null)
    {
        Console.WriteLine(s3);
    }
    else
    {
        Console.WriteLine("Incompatible cast");
    }
}
~~~

結果：

    Iverson Hong
    Incompatible cast


範例 2:

~~~csharp
class MyClass : MyBase, MyInterface
{
    public void MyInterfaceMethod()
    {
        Console.WriteLine("This is MyClass:MyInterfaceMethod()");
    }
}

class MyBase
{
    public virtual void MyBaseMethod()
    {
        Console.WriteLine("This is MyBase:MyBaseMethod()");
    }
}

interface MyInterface
{
    void MyInterfaceMethod();
}
~~~

~~~csharp
private void button2_Click(object sender, EventArgs e)
{
    MyClass myClass = new MyClass();
    MyInterface myInterface = myClass as MyInterface;
    if (myInterface != null)
    {
        myInterface.MyInterfaceMethod();
    }
    else
    {
        Console.WriteLine("Incompatible cast");
    }

    MyBase myBase = myClass as MyBase;
    if (myBase != null)
    {
        myBase.MyBaseMethod();
    }
    else
    {
        Console.WriteLine("Incompatible cast");
    }
}
~~~

結果：

    This is MyClass:MyInterfaceMethod()
    This is MyBase:MyBaseMethod()
    
----------

## is operator

1. 不進行轉換，只是判斷是否可轉型，回傳 bool，不會產生 exception

2. 常與強制轉型一起使用

範例 1:

~~~csharp
private void button3_Click(object sender, EventArgs e)
{
    int myInt = 0;
    bool compatible = myInt is int;
    System.Console.WriteLine("myInt is int = " + compatible);

    compatible = myInt is long;
    System.Console.WriteLine("myInt is long = " + compatible);

    compatible = myInt is float;
    System.Console.WriteLine("myInt is float = " + compatible);

    compatible = myInt is object;
    System.Console.WriteLine("myInt is object = " + compatible);
}
~~~

結果：

    myInt is int = True
    myInt is long = False
    myInt is float = False
    myInt is object = True

範例 2:

~~~csharp
private void button4_Click(object sender, EventArgs e)
{
    object s1 = "Iverson Hong";
    if (s1 is string)
    {
        string s2 = (string)s1;
        Console.WriteLine(s2);
    }
    else
    {
        Console.WriteLine("Incompatible cast");
    }

    if (s1 is StringBuilder)
    {
        StringBuilder s3 = (StringBuilder)s1;
        Console.WriteLine(s3);
    }
    else
    {
        Console.WriteLine("Incompatible cast");
    }
}
~~~

結果：

    Iverson Hong
    Incompatible cast

範例 3:

~~~csharp
private void button5_Click(object sender, EventArgs e)
{
    MyClass myClass = new MyClass();
    if (myClass is MyInterface)
    {
        ((MyInterface)myClass).MyInterfaceMethod();
    }
    else
    {
        Console.WriteLine("Incompatible cast");
    }

    if (myClass is MyBase)
    {
        ((MyBase)myClass).MyBaseMethod();
    }
    else
    {
        Console.WriteLine("Incompatible cast");
    }
}
~~~

結果：

    This is MyClass:MyInterfaceMethod()
    This is MyBase:MyBaseMethod()