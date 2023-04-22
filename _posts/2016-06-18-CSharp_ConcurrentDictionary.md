---
layout: post
title: C# ConcurrentDictionary
author: Yu-Qiao Hong
modified: 2016-06-18
tags: [C#]
---

在處理 multi thread 時最常碰到的就是 thread-safe 的問題，以下以簡單範例說明:

宣告一 Dictionary 並在 main thread 之外建立另外三個 thread：

1. 一個增加 Dictionary item
2. 一個印出現在 Dictionary 內有多少數量
3. 一個刪除 Dictionary 內第一個 item

## 沒有 thread-safe 的範例

~~~csharp
static void Main(string[] args)
{
    Dictionary<string, string> dict = new Dictionary<string, string>();
    bool running = true;
            
    Task.Factory.StartNew(() =>
    {
        while (running)
        {
            dict.Add(Guid.NewGuid().ToString(), "test");
        }
    });

    Task.Factory.StartNew(() =>
    {
        while (running)
        {
            Console.WriteLine("Dictionary Count={0}", dict.Count);
            Thread.Sleep(1000);
        }
    });

    Task.Factory.StartNew(() =>
    {
        while (running)
        {
            if (dict.Count > 0)
            {
                string key = dict.Keys.First();
                dict.Remove(key);
            }
        }
    });

    Console.ReadLine();
    running = false;
}
~~~

執行起來什麼亂七八糟的 Exception 都出現了，因為每個 thread 都對 Dictionary 容器操作，這就是典型的 **沒** 處理 thread-safe，一般來說，我們習慣使用 "**lock**" 來處理，在 Add() 或 Remove() 時限制其他 thread 進行操作。

----------

## 使用 lock

~~~csharp
static void Main(string[] args)
{
    Dictionary<string, string> dict = new Dictionary<string, string>();
    bool running = true;
            
    Task.Factory.StartNew(() =>
    {
        lock (dict)
        {
            while (running)
            {
                dict.Add(Guid.NewGuid().ToString(), "test");
            }
        }
    });

    Task.Factory.StartNew(() =>
    {
        while (running)
        {
            Console.WriteLine("Dictionary Count={0}", dict.Count);
            Thread.Sleep(1000);
        }
    });

    Task.Factory.StartNew(() =>
    {
        while (running)
        {
            lock (dict)
            {
                if (dict.Count > 0)
                {
                    string key = dict.Keys.First();
                    dict.Remove(key);
                }
            }
        }
    });

    Console.ReadLine();
    running = false;
}
~~~

## 結果

    Dictionary Count=41
    Dictionary Count=839797
    Dictionary Count=1675168
    Dictionary Count=2521714
    Dictionary Count=3320396
    Dictionary Count=4220085

使用 lock 解決了同時存取操作的問題，但缺點也就是要是在 **每一個操作** 加上 lock 判斷，若其中一個操作漏掉 lock，就不能保證有 thread-safe 了，因此.NET 4.0 提供了 "**ConcurrentDictionary**" 類別，就是 Dictionary thread-safe 版本。操作大致上與 Dictionary 相同。

----------

## 使用 ConcurrentDictionary

~~~csharp
static void Main(string[] args)
{
    ConcurrentDictionary<string, string> dict = new ConcurrentDictionary<string, string>();
    bool running = true;
            
    Task.Factory.StartNew(() =>
    {
        while (running)
        {
            dict.TryAdd(Guid.NewGuid().ToString(), "test");
        }
    });

    Task.Factory.StartNew(() =>
    {
        while (running)
        {
            Console.WriteLine("Dictionary Count={0}", dict.Count);
            Thread.Sleep(1000);
        }
    });

    Task.Factory.StartNew(() =>
    {
        while (running)
        {
            if (dict.Count > 0)
            {
                string key = dict.Keys.First();
                string dafault;
                dict.TryRemove(key, out dafault);
            }
        }
    });

    Console.ReadLine();
    running = false;
}
~~~

## 結果

    Dictionary Count=0
    Dictionary Count=5643
    Dictionary Count=9400
    Dictionary Count=11969
    Dictionary Count=13998
    Dictionary Count=15994
    
----------

## Reference ##

- [http://blog.darkthread.net/post-2014-04-15-concurrentdictionary.aspx](http://blog.darkthread.net/post-2014-04-15-concurrentdictionary.aspx)
- [http://dotnetpattern.com/csharp-concurrentdictionary](http://dotnetpattern.com/csharp-concurrentdictionary)