---
layout: post
title: C# 減少建構子初始化時的重覆邏輯
author: Yu-Qiao Hong
modified: 2016-09-16
tags: [C#, Effective C#]
---

在 C# 中若要建立多個建構子，除了可以用 multiple overloads 之外還可使用 default parameter(C# 4.0後提供)

以下使用 overload 方式產生 3 種不同的建構子：

1. 無參數

2. 帶入 1 個參數

3. 帶入 2 個參數

~~~csharp
class MyClass
{
    private int count;
    private string address;

    public MyClass() :
        this(0, "")
    {
    }

    public MyClass(string address) :
        this(0, address)
    {
    }

    public MyClass(int count, string address)
    {
        this.count = count;
        this.address = address;
    }
}
~~~

----------

在 C# 4.0 後可使用 default parameter 的方式來取代 overload：

~~~csharp
class MyClass1
{
    private int count;
    private string address;

    public MyClass1() :
        this(0, string.Empty)
    {
    }

    public MyClass1(int count = 0, string address = "")
    {
        this.count = count;
        this.address = address;
    }
}
~~~

以上述程式為例，使用 default parameter 雖有較高的彈性(有4個建構子：沒參數的、帶入name參數、帶入address參數、帶入兩個參數)，但在使用時須注意所有的組合以及預設值皆需合理且不會有非預期的邏輯產生，也因此與用戶端產生較高的耦合性。

----------

若不使用上述兩種方式，而只是把公用的程式抽出來：

~~~csharp
class MyClass2
{
    private int count;
    private string address;

    public MyClass2() 
    {
        SetParameters(0, "");
    }

    public MyClass2(string address)
    {
        SetParameters(0, address);
    }

    public MyClass2(int count, string address)
    {
        SetParameters(count, address);
    }

    private void SetParameters(int count, string address)
    {
        this.count = count;
        this.address = address;
    }
}
~~~

這種方式看起來好像沒什麼差別，但透過 IL 得知 3 個建構子內部都會先對成員變數進行初始化後再呼叫 SetParameters()，這是很沒效率的做法；另外兩種方式則是透過 this 把成員變數初始化的動作分派到其他的建構子中進行，此種方法可確保成員變數初始化只會做一次。

除了效能的考量外，若是成員變數為"**readonly**"時，也可在建構子來設定 readonly 初始值。

----------

[C# 成員變數(Member Variables)建立初始值的時間點](http://yu-qiao-hong.github.io//2016/08/07/CSharp_Effective_Item12.html)

[C# 在初始化時使用靜態成員以及靜態建構子](http://yu-qiao-hong.github.io/2016/09/12/CSharp_Effective_Item13.html)

總結這篇與上述兩篇，整理產生 class instance 時的順序：

1. 將 static 成員變數空間設為 0

2. 初始化 static 變數

3. 執行 base static class 的建構子

4. 執行 static class 建構子

5. 將 class 成員變數空間設為 0

6. 初始化成員變數

7. 執行 base class 建構子

8. 執行 class 建構子

產生相同 class 的 instance 時，就直接從步驟 5 開始，因 static 只會做一次。總結來說若無邏輯可直接對成員變數進行初始化，若有較複雜的邏輯可把初始化的行為寫在建構子內。