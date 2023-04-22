---
layout: post
title: C# yield
author: Yu-Qiao Hong
modified: 2016-09-17
tags: [C#]
---

yield 翻譯成中文有退讓、讓位的意思。以程式的觀點來看，它就是先把結果丟出去給外部處理，以實際範例說明：
有一內容為 1-10 的 int 集合，使用一般以及 yield 的方式取得其平方集合。

~~~csharp
static void Main(string[] args)
{
    List<int> numberList = new List<int>();
    for (int i = 1; i < 11; i++)
    {
        numberList.Add(i);
    }

    IEnumerable<int> commonList = CommonReturn(numberList);
    foreach (int num in commonList)
    {
        Console.WriteLine(num);
    }

    IEnumerable<int> yieldList = YieldReturn(numberList);
    foreach (int num in yieldList)
    {
        Console.WriteLine(num);
    }
}
~~~

~~~csharp
private static IEnumerable<int> CommonReturn(List<int> numberList)
{
    List<int> returnList = new List<int>();
    foreach (var num in numberList)
    {
        returnList.Add(num * num);
    }

    return returnList;
}
~~~

~~~csharp
private static IEnumerable<int> YieldReturn(List<int> numberList)
{
    foreach (var num in numberList)
    {
        yield return num * num;
    }
}
~~~

兩種方式結果都一樣，但 yield 的流程不同於一般流程，以圖示來解釋

![]({{ "/images/CSharp_Yield/001.png" | relative_url}})

步驟 2 每一次執行 foreach 都會從 YieldReturn() 內取出一個平方值再印出其內容。因此執行順序為 1->2->3->4->2->3->4... 直到 numberList 取完。

yield 的回傳值必須為 IEnumerable、IEnumerator、IEnumerable<T>或IEnumerator<T>。若自訂的 class 繼承 IEnumerable 卻不使用 yield 的話，必須自己實作 IEnumerator。

~~~csharp
public interface IEnumerable
{
    IEnumerator GetEnumerator();
}
~~~

~~~csharp
public interface IEnumerator
{
    object Current { get; }
    bool MoveNext();
    void Reset();
}
~~~

![]({{ "/images/CSharp_Yield/002.png" | relative_url}})

----------