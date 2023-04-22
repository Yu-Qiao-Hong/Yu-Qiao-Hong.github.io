---
layout: post
title: C# checked/unchecked
author: Yu-Qiao Hong
modified: 2016-11-28
tags: [C#]
---　　　　　　　　　　　

"**checked**" 關鍵字是用來檢查整數類資料型別(Integral Type)進行算術運算或轉換時的溢位檢查。在一般情況下，並不會檢查數值是否發生 overfolw，若希望在執行階段判斷是否產生 overfolw 則可使用 "**checked**" 關鍵字。

如下列程式碼，執行並不會發生任何的錯誤，而是直接 overfolw 變成負數：

~~~csharp
static void Main(string[] args)
{
    // int.MaxValue equals 2147483647
    int n = int.MaxValue;
    n = n + 1;
    System.Console.WriteLine(n);
}
~~~

輸出結果：

~~~
-2147483648
~~~

----------

使用 "checked"

~~~csharp
static void Main(string[] args)
{
    checked
    {
        // int.MaxValue equals 2147483647
        int n = int.MaxValue;
        n = n + 1;
        System.Console.WriteLine(n);
    }
}
~~~

![]({{ "/images/CSharp_Checked_Unchecked/001.png" | relative_url}})

發生 overflow，跳出 exception 視窗

----------

補上 try catch

~~~csharp
class Program
{
    static void Main(string[] args)
    {
        try
        {
            checked
            {
                // int.MaxValue equals 2147483647
                int n = int.MaxValue;
                n = n + 1;
                System.Console.WriteLine(n);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }
}
~~~

輸出結果：

~~~
數學運算導致溢位。
~~~