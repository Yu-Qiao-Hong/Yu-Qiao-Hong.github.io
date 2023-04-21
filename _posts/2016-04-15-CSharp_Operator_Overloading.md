---
layout: post
title: C# Operator Overloading
author: Yu-Qiao Hong
modified: 2016-04-16
tags: [C#]
---
## 使用時機

定義一個 BankAccount class，描述這銀行帳戶的擁有者及其金額

~~~csharp
class BankAccount
{
    private string _owner;
    private double _money;

    public BankAccount(string owner, double money)
    {
        _owner = owner;
        _money = money;
    }
}
~~~

接著在clint產生兩個實體:

~~~csharp
BankAccount account1 = new BankAccount("Iverson Hong", 1000);
BankAccount account2 = new BankAccount("Michael Jordan", 2000);
~~~

接下來我想要些後續操作:

1. 我想要比較兩個戶頭裡的錢誰比較多。

2. 我想要把兩個戶頭的錢存在一個共同戶頭裡。

3. 我想要存錢。

4. 我想要從銀行帳戶取得帳戶擁有者名稱。

簡單啊!程式馬上寫出來(當然方法很多，以下寫法只是為了講解 operator overloading)

~~~csharp
// 1. Compare
if (account1 > account2)
    Console.WriteLine("account1's money is more than account2");
else
    Console.WriteLine("account2's money is more than account1");

// 2. Add together
BankAccount account3 = account1 + account2;

// 3. Save
account3 = account3 + 500;

// 4. Get owner name from account class
string name = account3;
~~~

寫完發現上述的四個描述沒有一個 build 的過，這個時候就是 operator overloading 該出場的時候了。

----------

## Operator Overloading

在 BankAccount 新增幾個 method，讓上面的程式碼可以正常的運作

**1. 我想要比較兩個戶頭裡的錢誰比較多。**

~~~csharp
// if (account1 > account2)
public static bool operator >(BankAccount left, BankAccount right)
{
    if (left._money > right._money)
        return true;
    else
        return false;
}

public static bool operator <(BankAccount left, BankAccount right)
{
    if (left._money < right._money)
        return true;
    else
        return false;
}
~~~

**2. 我想要把兩個戶頭的錢存在一個共同戶頭裡。**

~~~csharp
// BankAccount account3 = account1 + account2;
public static BankAccount operator +(BankAccount left, BankAccount right)
{
    return new BankAccount(left._owner + "+" + right._owner, left._money + right._money);
}
~~~

**3. 我想要存錢。**

~~~csharp
// account3 = account3 + 500;
public static BankAccount operator +(BankAccount left, int right)
{
    return new BankAccount(left._owner, left._money + right);
}
~~~

**4. 我想要從銀行帳戶取得帳戶擁有者名稱。**

~~~csharp
// string name = account3;
public static implicit operator string(BankAccount account)
{
    return account._owner;
}
~~~

這邊需要注意的是，沒有辦法只寫 **"operator >"** method，需把另一個 **"operator <"** 也實做出來才可 build 過。