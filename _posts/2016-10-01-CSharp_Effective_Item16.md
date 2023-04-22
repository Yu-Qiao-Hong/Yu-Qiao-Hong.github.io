---
layout: post
title: C# 避免產生非必要的實體化
author: Yu-Qiao Hong
modified: 2016-10-01
tags: [C#, Effective C#]
---

雖然 GC 可以自動回收資源，但若在程式中產生過多的 reference type 實體(占用heap空間)，配置以及銷毀這些物件還是需要時間來處理。因此必須有效的創建這些實體，避免非必要的產生。

以OnPaint()為例：

~~~csharp
protected override void OnPaint(PaintEventArgs e)
{
    // Bad. Created the same font every paint event.
    using (Font myFont = new Font("Arial", 10.0f))
    {
        e.Graphics.DrawString(DateTime.Now.ToString(),
        myFont, Brushes.Black, new PointF(0, 0));
    }
    base.OnPaint(e);
}
~~~

OnPaint() 會頻繁的被呼叫使用，每一次呼叫都會創建一個 Font 實體，結束時會通知 GC 進行資源回收，此種寫法很明顯沒有效率。可以將創建 Font 的動作改放到 member variable：

~~~csharp
private readonly Font myFont = new Font("Arial", 10.0f);

protected override void OnPaint(PaintEventArgs e)
{
    e.Graphics.DrawString(DateTime.Now.ToString(),
    myFont, Brushes.Black, new PointF(0, 0));
    base.OnPaint(e);
}
~~~

這樣被呼叫時不會一直創建新的 Font 實體，但須注意的是，搬到 member variable 的 class 若有繼承 IDisposable 記得必須實作。也並非要把所有的區域變數都改成 member variable，是要頻繁調用的 function 這樣才有改才有意義

----------

以上述為例，想使用一個黑色筆刷在專案內的多個視窗中使用，雖然已經抽到 member variable，但在各個視窗類別中還是有許多相同的黑色筆刷物件，這時可以使用 singleton 來確保只會創建一份實體：

~~~csharp
private static Brush blackBrush;

public static Brush Black
{
    get
    {
        if (blackBrush == null)
            blackBrush = new SolidBrush(Color.Black);

        return blackBrush;
    }
}
~~~

----------

除了上述抽到 member variable 以及 singleton 外，還需盡量減少使用不可更改的類別(代表每次 assignment 都會產生新的實體)，System.String 就是不可更改的類別

~~~csharp
string msg = "Hello, ";
msg += thisUser.Name;
msg += ". Today is ";
msg += System.DateTime.Now.ToString();
~~~

看起來沒什麼問題，其實效率非常低，等同於以下寫法：

~~~csharp
string msg = "Hello, ";
// Not legal, for illustration only:
string tmp1 = new String(msg + thisUser.Name);
msg = tmp1; // "Hello " is garbage.
string tmp2 = new String(msg + ". Today is ");
msg = tmp2; // "Hello <user>" is garbage.
string tmp3 = new String(msg + DateTime.Now.ToString());
msg = tmp3; // "Hello <user>. Today is " is garbage.
~~~

因不可更改所以每次的 += 就會創建一個新的 string 實體返回，tmp1、tmp2、tmp3 和最一開始的 msg 這 4 個字串都變成垃圾等待回收。應改寫成以下：

~~~csharp
string msg = string.Format("Hello, {0}. Today is {1}", thisUser.Name, DateTime.Now.ToString());
~~~

若較複雜的字串處理，建議可以使用 StringBuilder，StringBuilder 為可變字串型態，在產生最終的 string 之前，可以對其內容進行修改而不會在過程中創建新的實體出來：

~~~csharp
StringBuilder msg = new StringBuilder("Hello, ");
msg.Append(thisUser.Name);
msg.Append(". Today is ");
msg.Append(DateTime.Now.ToString());
string finalMsg = msg.ToString();
~~~