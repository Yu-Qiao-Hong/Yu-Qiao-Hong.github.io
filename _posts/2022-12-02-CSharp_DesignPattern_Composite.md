---
layout: post
title: C# Composite Pattern
author: Yu-Qiao Hong
modified: 2022-12-02
tags: [C#, Design Pattern]
---

## 目的/使用時機

* 處理樹狀階層的架構，讓客戶端能使用一致的方式操作整個樹狀結構內容。

----------

## UML

### Uniformity

![]({{ "/images/CSharp_DesignPattern_Composite/class-diagram-uniformity.png" | relative_url}})

### Type Safety

![]({{ "/images/CSharp_DesignPattern_Composite/class-diagram-type-safety.png" | relative_url}})

---

## 說明

組合模式有兩種：

Uniformity，也有人說透明式，於介面(Component)內宣告管理子物件的方法(Add(), Remove())，好處是 client 不需要分辨 Leaf 還是 Composite，對 client 來說操作方式都是一致的(透明的)；缺點是 Leaf 並不需要實作 Add(), Remove()等管理子物件的方法...，client 有可能會誤操作，所以較有安全性的疑慮。

另一種為 Type Safety，將管理子物件的操作下放到 Composite，缺點為 client 調用時需增加對應的判斷。

----------

## 優點

* 透過使用多型及遞迴來處理複雜的樹狀結構。

* 遵循 component interface 則可導入新的 element，不需要修改既有的 client 邏輯 ，符合開放封閉原則。

----------

## 缺點

* 當節點 element 的特性差異較大時，可能會訂出較複雜的 component interface(為了符合所有 Leaf 或 Composite)，增加程式理解複雜度。

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

- [https://refactoring.guru/design-patterns/composite](https://refactoring.guru/design-patterns/composite)