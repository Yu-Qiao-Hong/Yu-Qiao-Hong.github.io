---
layout: post
title: C# Proxy Pattern
author: Yu-Qiao Hong
modified: 2022-12-04
tags: [C#, Design Pattern]
---

## 目的/使用時機

* client 不能直接間觸到對向本身，則透過中間的代理人來做溝通。

* 透過 wrapper 的方式以保護實際組件，並可在訪問實作些邏輯控制(也可設計為 Cache)

----------

## Participants

* Subject: 提供 RealSubject 和 Proxy 的公共介面。

* Proxy: 裡面具有 RealSubject 的實體，代理可先預處理(access control, cache...)後再呼叫實際服務。

* RealSubject: 真正提供服務的物件。

----------

## UML

![]({{ "/images/CSharp_DesignPattern_Composite/class-diagram-uniformity.png" | relative_url}})

---

## 說明

Proxy 有幾種常見的用法：

*

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

## 比較

* Adapter 為被包裝對象提供了不同的接口，Proxy 為其提供了相同的接口，Decorator 為其提供了增強的接口。

## Reference

- [https://refactoring.guru/design-patterns/composite](https://refactoring.guru/design-patterns/composite)