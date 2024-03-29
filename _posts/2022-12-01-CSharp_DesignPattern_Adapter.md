---
layout: post
title: C# Adapter Pattern
author: Yu-Qiao Hong
modified: 2022-12-01
tags: [C#, Design Pattern]
---

## Real Word Example

{% include aligner.html images="../../images/CSharp_DesignPattern_Adapter/adapter.png,../../images/CSharp_DesignPattern_Adapter/real-word-adapter.webp" column=2 %}

---

## 目的/使用時機

* 讓原來不兼容的介面可以互相偕同工作。(如無法修改或增加內容的 existing class， 或第三方 class，視為 adaptee)

* 將既有的介面轉換為客戶端能使用的介面。(保留/不修改 client 端使用方式)

* 實作方式為 wrap 既有的 class 成新的 class (adapter)。

----------

## UML

![]({{ "/images/CSharp_DesignPattern_Adapter/class-diagram.png" | relative_url}})

----------

## 優點

* 遵循 client interface 而去實作 adapter，不會動到既有 client 與 adaptee ，符合開放封閉原則。

----------

## 缺點

* 增加複雜度，需增加一個 client interface 與 adapter class。

* 有些時候可簡單修改 service class(adaptee)即可供 client 使用。

----------

## Reference

- [https://refactoring.guru/design-patterns/adapter](https://refactoring.guru/design-patterns/adapter)