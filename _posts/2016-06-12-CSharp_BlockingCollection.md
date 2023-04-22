---
layout: post
title: C# Producer Consumer Design Pattern 以 BlockingCollection 來實現
author: Yu-Qiao Hong
modified: 2016-06-12
tags: [C#, Design Pattern]
---

在處理 multi thread 時有很大的機會需使用 producer consumer pattern，以下以貨物簡單定義 producer consumer pattern：

1. producer 與 consumer 為兩個獨立的執行序(producer 生產貨物速度與 consumer 消耗貨物速度不會同步)
2. producer 與 consumer 中間有一通用容器供彼此交換(貨架)
3. 此通用容器有儲存上限(貨架是固定大小)
4. 當容器滿時，producer 則停止生產，直到容器出現空間(貨架擺滿了貨物，則不需補貨)
5. 當容器全為空時，consumer 則停止取出，直到容器內有東需可取出(貨架上沒東西時，consumer 無法從貨架取東西)

----------

c# 中有提供 **BlockingCollection** 類別可簡單實現 producer consumer pattern：

假設 Producer 共有 20 件貨物，貨架上最多只能擺 3 件貨物；有兩個 Consumer 從貨架取出貨物

## Code

~~~csharp
using System.Collections.Concurrent;

static void Main(string[] args)
{
    BlockingCollection<int> b = new BlockingCollection<int>(3);
    Task producerTask = Task.Factory.StartNew(() =>
    {
        for (int i = 0; i < 20; i++)
        {
            b.Add(i);
            //Thread.Sleep(50);
            Console.WriteLine("Producer add item {0}", i.ToString());
        }

        b.CompleteAdding();
        Console.WriteLine("CompleteAdding");
    });

    Task consumer1Task = Task.Factory.StartNew(() =>
    {
        foreach(int item in b.GetConsumingEnumerable())
        {
            Console.WriteLine("consumer1 take item {0}", item.ToString());
        }
    });

    Task consumer2Task = Task.Factory.StartNew(() =>
    {
        foreach (int item in b.GetConsumingEnumerable())
        {
            Console.WriteLine("consumer2 take item {0}", item.ToString());
        }
    });

    Task.WaitAll(producerTask, consumer1Task, consumer2Task);
    Console.WriteLine("Done!");
}
~~~

## 結果

    Producer add item 0
    Producer add item 1
    Producer add item 2
    Producer add item 3
    Producer add item 4
    consumer2 take item 1
    consumer2 take item 2
    consumer1 take item 0
    consumer1 take item 4
    consumer1 take item 5
    Producer add item 5
    Producer add item 6
    Producer add item 7
    consumer1 take item 6
    consumer1 take item 7
    consumer1 take item 8
    consumer2 take item 3
    Producer add item 8
    Producer add item 9
    Producer add item 10
    Producer add item 11
    Producer add item 12
    Producer add item 13
    consumer1 take item 9
    consumer1 take item 11
    consumer1 take item 12
    consumer1 take item 13
    consumer1 take item 14
    consumer2 take item 10
    Producer add item 14
    consumer1 take item 15
    Producer add item 15
    Producer add item 16
    Producer add item 17
    Producer add item 18
    Producer add item 19
    consumer2 take item 16
    consumer2 take item 18
    consumer2 take item 19
    CompleteAdding
    consumer1 take item 17
    Done!

## 解說

~~~csharp
b.CompleteAdding();
~~~

代表 Producer 已經完成動作，不會再 add 任何 item 到 collection 內，若不呼叫則

~~~csharp
b.GetConsumingEnumerable()
~~~

永遠等待，因此兩個 consumer task 則永遠無法結束。

從輸出結果可看出 Producer 最多只能放入3件貨物，直到有 Consumer 取出貨物；把 add 訊息拿掉則可更清楚的看見其結果

    consumer2 take item 1
    consumer2 take item 2
    consumer1 take item 0
    consumer1 take item 4
    consumer1 take item 5
    consumer1 take item 6
    consumer1 take item 7
    consumer1 take item 8
    consumer2 take item 3
    consumer1 take item 9
    consumer1 take item 11
    consumer1 take item 12
    consumer1 take item 13
    consumer1 take item 14
    consumer2 take item 10
    consumer1 take item 15
    consumer2 take item 16
    consumer2 take item 18
    consumer2 take item 19
    CompleteAdding
    consumer1 take item 17
    Done!
    
由上述範例得知

Consumer1 取得貨物:4,5,6,7,8,9,11,12,13,14,15,17

Consumer2 取得貨物:1,2,3,10,16,18,19

----------

## Reference

- [http://dotnetpattern.com/csharp-blockingcollection](http://dotnetpattern.com/csharp-blockingcollection)
- [https://msdn.microsoft.com/zh-tw/library/dd997371(v=vs.110).aspx](https://msdn.microsoft.com/zh-tw/library/dd997371(v=vs.110).aspx)