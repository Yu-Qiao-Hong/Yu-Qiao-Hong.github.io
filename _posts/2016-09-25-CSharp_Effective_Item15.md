---
layout: post
title: C# 使用using和try/finally來釋放資源
author: Yu-Qiao Hong
modified: 2016-09-25
tags: [C#, Effective C#]
---

在 C++ 程式中可使用 destructor 來釋放資源，而 C# 中則透過 GC(Garbage Collector) 來自動回收資源，GC 為 CLR 內的機制之一，凡是在 CLR 所運作中的程式都可稱為 managed code，不是在 CLR運作中的程式就稱為 unmanaged code(stream、與資料庫的連結、COM物件...等)。也就是說若是使用了 unmanaged code 後並沒有釋放資源，則會等到程式結束時才釋放。

在 C# 中使用 unmanaged code 想要釋放資源必須要使用者明確的寫出釋放資源的方法，也就是使用 Finalize() 和 Dispose()。呼叫 Finalize() 並不會馬上執行而是交給 GC 來處理，這邊不多做討論；而 Dispose() 則可是明確通知 GC 進行資源回收。可透過 using statement 以及 try/finally 來呼叫 Dispose()。

以執行 sql 程式為例：

~~~csharp
public void ExecuteCommand(string connString, string commandString)
{
    SqlConnection myConnection = new SqlConnection(connString);
    SqlCommand mySqlCommand = new SqlCommand(commandString, myConnection);

    myConnection.Open();
    mySqlCommand.ExecuteNonQuery();
}
~~~

SqlConnection 以及 SqlCommand 的實體會一直存在記憶體直到程式結束。因此可再修改為：

~~~csharp
public void ExecuteCommand(string connString, string commandString)
{
    SqlConnection myConnection = new SqlConnection(connString);
    SqlCommand mySqlCommand = new SqlCommand(commandString, myConnection);

    myConnection.Open();
    mySqlCommand.ExecuteNonQuery();

    mySqlCommand.Dispose();
    myConnection.Dispose();
}
~~~

還是有可能發生問題，若是在呼叫 Dispose() 之前發生 exception，則永遠不會釋放記憶體，因此可使用 using statement 來確保 Dispose() 一定會被呼叫到：

~~~csharp
public void ExecuteCommand(string connString, string commandString)
{
    using (SqlConnection myConnection = new SqlConnection(connString))
    {
        using (SqlCommand mySqlCommand = new SqlCommand(commandString, myConnection))
        {
            myConnection.Open();
            mySqlCommand.ExecuteNonQuery();
        }
    }
}
~~~

也可用 try/finally 來改寫上述程式，其 IL 內容與使用 using statement完全一樣：

~~~csharp
public void ExecuteCommand(string connString, string commandString)
{
    SqlConnection myConnection = null;
    SqlCommand mySqlCommand = null;
    try
    {
        myConnection = new SqlConnection(connString);
        try
        {
            mySqlCommand = new SqlCommand(commandString, myConnection);
            myConnection.Open();
            mySqlCommand.ExecuteNonQuery(); 
        }
        finally
        {
            if (mySqlCommand != null)
                mySqlCommand.Dispose();
        }
    }
    finally
    {
        if (myConnection != null)
            myConnection.Dispose();
    }
}
~~~

也就是一個 using statement 可以用一個 try/finally來改寫。

----------

若 using statement 內的類別沒有支援 IDisposable interface，在編譯時就會產生錯誤：

~~~csharp
using (string msg = "This is a message")
{
    Console.WriteLine(msg);
}
~~~

![]({{ "/images/CSharp_Effective_Item15/001.png" | relative_url}})

----------

在編譯時期就知道有支援 IDisposable interface 的型態才可使用 using statement，在編譯時期無法確認則無法使用：

~~~csharp
using (object obj = Factory.CreateResource())
    Console.WriteLine(obj.ToString());
~~~

但可透過[as statement轉型](http://yu-qiao-hong.github.io/2016/07/10/CSharp_Effective_Item3.html)後使用

~~~csharp
object obj = Factory.CreateResource();
using (obj as IDisposable)
{
    Console.WriteLine(obj.ToString());
}
~~~