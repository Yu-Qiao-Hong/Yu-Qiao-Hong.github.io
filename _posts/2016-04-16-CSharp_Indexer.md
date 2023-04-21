---
layout: post
title: C# Indexer
author: Yu-Qiao Hong
modified: 2016-04-16
tags: [C#]
---

## 使用時機

定義一個 BasketballPlayer class，裡面描述前 10 名我所喜愛的球員

~~~csharp
class BasketballPlayer
{
    public static int SIZE = 10;
    private string[] favoritePlayers;

    public BasketballPlayer()
    {
        favoritePlayers = new string[SIZE];
    }
}
~~~

接著我想設定喜愛球員的排名，並把名單顯示出來。我當然可以寫個 SetPlayer(int No, string Name) 以及 ShowList()，但我現在想直接利用 new 出來的 class 直接以處理陣列的方式做設定。

~~~csharp
BasketballPlayer playerList = new BasketballPlayer();

playerList[1] = "Allen Iverson";
playerList[2] = "Michael Jordan";
playerList[5] = "Steve Nash";

for (int i = 0; i < BasketballPlayer.SIZE; i++)
{
    if(playerList[i] != null)
        Console.WriteLine(i.ToString() + ": " + playerList[i]);
}
~~~

理所當然編譯不過，因為 playerList 是一個 class 不是一個 array，這時候就可以使用 indexer 了。

----------

## Indexer

在 BasketballPlayer 裡加入以下 method，讓上面的程式碼可正常運作

~~~csharp
public string this[int i]
{
    get
    {
        if (i >= 0 && i < SIZE)
            return favoritePlayers[i];
        else
            return "";
    }
    set
    {
        if (i >= 0 && i < SIZE)
            favoritePlayers[i] = value;
    }
}
~~~

如此一來不就跟使用 array 一樣方便了呢?