---
layout: post
title: C# 為 class 提供有意義的 ToString() method
author: Yu-Qiao Hong
modified: 2016-07-14
tags: [C#, Effective C#]
---

C# 所有的 class 都繼承 Object class，其內有一 virtual ToString() method。若不 override，則會印出此 class 的名稱，其資訊對使用者來說並不重要。

~~~csharp
class Test
{
}

private void button1_Click(object sender, EventArgs e)
{
    Test test = new Test();
    Console.WriteLine(test.ToString());
}

private void button2_Click(object sender, EventArgs e)
{
    List<int> list = new List<int>();
    Console.WriteLine(list.ToString());
}
~~~

分別執行結果為：

    ConsoleApplication1.Test
    System.Collections.Generic.List`1[System.Int32]

可 override ToString() 使其更有意義

~~~csharp
class Test
{
    public override string ToString()
    {
        return "This is override ToString()";
    }
}

private void button1_Click(object sender, EventArgs e)
{
    Test test = new Test();
    Console.WriteLine(test.ToString());
}
~~~

結果：

    This is override ToString()
    
除此之外在 C# 中許多操作也會隱性的呼叫 ToString()，如 Console.WriteLine

~~~csharp
private void button2_Click(object sender, EventArgs e)
{
    Test test = new Test();
    Console.WriteLine(test);
}
~~~

結果跟上一個例子一樣。