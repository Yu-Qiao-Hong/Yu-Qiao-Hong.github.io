---
layout: post
title: C# Composite Pattern
author: Yu-Qiao Hong
modified: 2022-12-02
tags: [C#, Design Pattern]
---

## 目的/使用時機

* 需要處理樹狀階層的架構(由子節點、父節點排列組成)。

* 讓客戶端能使用一致的方式操作整個樹狀結構內容。

----------

## Participants

* Component: 定義此樹狀結構的操作介面，用來給 Leaf/Composite 繼承使用。

* Leaf: 作為樹狀階層的最後節點，其內沒有 child components。

* Composite: 可視為 container，裡面有 child components(由 Leaf 或其他 composite 組成)，會實作管理子物件的方法，如 Add(Component), Remove(Component)。

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

* client 端使用方式較統一。

* 透過使用多型及遞迴來處理複雜的樹狀結構。

* 遵循 component interface 則可導入新的 element，不需要修改既有內容 ，符合開放封閉原則。

----------

## 缺點

* 當節點 element 的特性差異較大時，可能會訂出較複雜的 component interface(為了符合所有 Leaf 或 Composite)，增加程式理解複雜度。

* 當使用 Uniformity 方式，須注意 Leaf 對於 Add, Remove 等相關操作不應實作(違反依賴反轉)。

----------

## Reference

- [https://refactoring.guru/design-patterns/composite](https://refactoring.guru/design-patterns/composite)