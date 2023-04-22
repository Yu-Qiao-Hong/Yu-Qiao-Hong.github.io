---
layout: post
title: C# 介紹各種相等(Equality)的用法
author: Yu-Qiao Hong
modified: 2016-07-16
tags: [C#, Effective C#]
---

在 C# 中若要使用 "**相等(Equality)**"，常見的有 4 種方式，以下一一介紹：

~~~csharp
public static bool ReferenceEquals(object objA, object objB);

public static bool Equals(object objA, object objB);

public virtual bool Equals(object obj);

public static bool operator ==(MyClass left, MyClass right);
~~~

## static Object.ReferenceEquals()

此方法不需 override，用來比較兩個 object 是否指向相同的 reference 位址，若是比較兩個 value type 物件，其結果必為 **False**，原因為 value type 要轉成 object(reference type) 此動作為 Boxing。

~~~csharp
static void Main(string[] args)
{
    int i = 5;
    int j = 5;

    if (Object.ReferenceEquals(i, j))
        Console.WriteLine("Never happens.");
    else
        Console.WriteLine("Always happens.");

    if (Object.ReferenceEquals(i, i))
        Console.WriteLine("Never happens.");
    else
        Console.WriteLine("Always happens.");
}
~~~

結果:

    Always happens.
    Always happens.

----------

## static Object.Equals()

此方法不需 override，在 runtime 時比較兩個值是否相等，其實際運作為

~~~csharp
public static new bool Equals(object left, object right)
{
    // Check object identity
    if (Object.ReferenceEquals(left, right))
        return true;

    // both null references handled above
    if (Object.ReferenceEquals(left, null) ||
        Object.ReferenceEquals(right, null))
        return false;

    return left.Equals(right);
}
~~~

簡單來說，兩個值為 reference type，則與 Object.ReferenceEquals() 相同，比較是否指向相同的 reference 位址；若是兩個比較值為 value type，則比較實際的內容值是否相等。

----------

## instance Object.Equals()

1. 此為可 override 的 method。
 
2. reference type 預設行為跟 Object.ReferenceEquals() 一樣，若此行為不是想要的則可 override。

3. 若為 boxing，例如 object a = 1, b = 1。其內型態為 int(value type)，此為多型，實際執行 Int32.Equals(Object)。
 
3. value type 則 override 比較是否為同類型且內容相等(預設利用反射，效率不高)。

接著以程式範例說明自定義的 class 如何 override Object.Equals()，需記得實作 IEquatable<T> 介面中的 Equals，才不會出現不預期的錯誤。

~~~csharp
public interface IEquatable<T>
{
    bool Equals(T other);
}
~~~

~~~csharp
public class Foo : IEquatable<Foo>
{
    public override bool Equals(object right)
    {
        // check null:
        // this pointer is never null in C# methods.
        if (object.ReferenceEquals(right, null))
            return false;

        if (object.ReferenceEquals(this, right))
            return true;

        // Discussed below.
        if (this.GetType() != right.GetType())
            return false;

        // Compare this type's contents here:
        return this.Equals(right as Foo);
    }

    #region IEquatable<Foo> Members
    public bool Equals(Foo other)
    {
        // elided.
        return true;
    }
    #endregion
}
~~~

在 override Equals()時，裡面不應該出現丟出任何 exception，只有相等 True，不相等則回傳 False 兩種結果。第一個判斷檢查是否為 null，再來判斷是否指向相同 reference位址(相同位址內表內容一定一樣)。接著利用反射判斷是否為相同型態(使用 this,而不直接使用 Foo，因可能為繼承 Foo 的 class，在繼承時會有兩邊結果不相等的問題產生，下面例子說明)，最後比較實際內容。

~~~csharp
class B : IEquatable<B>
{
    public override bool Equals(object right)
    {
        // ...
     
        B rightAsB = right as B;
        if(rightAsB == null)
            return false;
         
        // ...
    }
}

class C : B, IEquatable<C>
{
    public override bool Equals(object right)
    {
        // ...
     
        C rightAsC = right as C;
        if(rightAsC == null)
            return false;
         
        // ...
    }
}


// Client

B baseObj = new B();
C derivedObj = new C();

// Comparison 1
if(baseObj.Equals(derivedObj))
    Console.WriteLine("Equals");
else
    Console.WriteLine("Not Equals");
    
// Comparison 2
if(derivedObj.Equals(baseObj))
    Console.WriteLine("Equals");
else
    Console.WriteLine("Not Equals");
~~~

上面兩個比較結果，要嘛都回傳Equals，要嘛都回傳 Not Equals，不應該兩個結果不同，但上面寫法在第二個比較必定回傳 Not Equals，原因為 B 為 base class，不可轉換為其衍生類別 C，所以回傳false。

結論為若是自定義的 value type，則需 override Equal();若是 reference type， Object.Equals() 不能滿足你的需求才須自己 override。

----------

## operator == ()

只要是 value type，皆需 redefine operator == ()，其原因與 instance Object.Equals() 一樣為預設使用反射比較兩個實際內容造成效率不高；但在自定義 reference type 時，應盡量減少redefine operator ==() 而應 override Equals() 使得語意更加清楚。

> (Notice that I didn’t say that you should write operator==() whenever you override instance Equals(). I said to write operator==() when you create value types. You should rarely override operator==() when you create reference types. The .NET Framework classes expect operator==() to follow reference semantics for all reference types.)

總結來說：

1. 正常情況下 reference type 比記憶體位置，string 則為例外，它是比實際內容。

2. value type 需 override 比較實際內容。

----------

## 總結

介紹 4 種相等的方法：

- static Object.ReferenceEquals() 與 static Object.Equals() 都提供了正確的判斷且與型態無關。

- value type：總是 override instance Object.Equals() 與 operator == () 比較實際內容，以便得到較好的效能。

- reference type：當你不想只是比記憶體位置時才去 override instance Object.Equals()。