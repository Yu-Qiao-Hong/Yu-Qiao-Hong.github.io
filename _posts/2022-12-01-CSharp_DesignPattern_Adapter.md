---
layout: post
title: C# Adapter Pattern
author: Yu-Qiao Hong
modified: 2022-12-01
tags: [C#, Design Pattern]
---

## 定義/使用時機

* 讓原來不兼容的介面可以互相偕同工作。

* 將既有的介面轉換為客戶端能使用的介面。

* 實作方式為 wrap 既有的 class 成新的 class。

----------

## UML

<div class="mermaid">
classDiagram

class Target{
    <<interface>>
    +Operation()
}

class Adapter{
    +Operation()
}

class Adaptee{
    +SpecificOperation()
}

Target  <|-- Adapter
Adaptee <--  Adapter
Client --> Target

</div>

![]({{ "/images/CSharp_DesignPattern_Adapter/real-word-adapter.webp" | relative_url}})

----------

## 優點

* 遵循 client interface 而去實作 adapter，不會動到既有 client 與 adaptee ，符合開放封閉原則。

----------

## 缺點

* 增加複雜度，需增加一個 client interface 與 adapter class。

* 有些時候可簡單修改 service class(adaptee)即可供 client 使用。

----------

## 範例

寫一漢堡類別，允許可動態地增加其多種配料，並計算出最後價錢。

### UML：


### 定義一漢堡虛擬類別 (Component)：
~~~c#

~~~

### 定義兩個基本款漢堡：起司堡、大麥克 (ConcreteComponent)：
~~~c#

~~~

### client 端：
~~~c#

~~~

### 輸出結果：
```

```

## Reference

- [https://refactoring.guru/design-patterns/adapter](https://refactoring.guru/design-patterns/adapter)