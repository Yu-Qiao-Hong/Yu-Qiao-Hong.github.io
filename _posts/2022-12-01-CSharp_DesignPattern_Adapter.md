---
layout: post
title: C# Adapter Pattern
author: Yu-Qiao Hong
modified: 2022-12-01
tags: [C#, Design Pattern]
---

![]({{ "/images/CSharp_DesignPattern_Adapter/adapter.png" | relative_url}})

## 目的/使用時機

* 讓原來不兼容的介面可以互相偕同工作。

* 將既有的介面轉換為客戶端能使用的介面。

* 實作方式為 wrap 既有的 class 成新的 class。

----------

## UML

![]({{ "/images/CSharp_DesignPattern_Adapter/class-diagram.png" | relative_url}})

<!---
<div class="mermaid">
classDiagram

Target  <-- Client
Target  <|-- Adapter
Adaptee <--  Adapter

class Target{
    <<interface>>
    +Operation()
}
note for Target "client interface"

class Adapter{
    +Operation()
}
note for Adapter "adaptee.SpecificOperation()"

class Adaptee{
    +SpecificOperation()
}
</div>
-->

## Real Word Example

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