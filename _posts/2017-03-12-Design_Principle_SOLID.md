---
layout: post
title: Design Principle - SOLID
author: Yu-Qiao Hong
modified: 2018-05-20
tags: [Design Pattern]
---

程式設計上遵循 SOLID 這五項基本原則，可寫出好維護易擴充的程式架構

## Single Responsibility Principle (SRP)

**單一職責**

> A class should have only one reason to change

一個 class 應有一個，且只有一個理由可使其改變。

一個 class 只專心做一件事。如一個 class 要負責過多職責，等於把這些職責耦合在一起，當其中一個職責變化或修改時，其他的職責可能發生意想不到的錯誤。

----------

## Open Closed Principle (OCP)

**開放封閉原則**

> Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification

軟體設計應該對擴充是開放的，但對修改是封閉的。希望在不修改現有程式碼的前提之下，增加新的程式來擴充新的功能。

理想上是這樣，但如何做到呢？首先要先清楚地建立程式模組間的上下階層關係，如 A 模組使用到 B 模組，A->B。
可使用抽象介面來隔離上下階層的關係，使得 A 不直接依賴 B，而是透過介面的方式來操作 B(高階模組不應該依賴於低階模組，應該依賴抽象)。

----------

## Liskov Substitution Principle (LSP)

**Liskov 替換原則**

> Subtypes must be substitutable for their base types.

子類別必須要能取代它的父類別。只有當子類別替換掉父類別且軟體行為不受到影響時，子類別才能在父類別的基礎上增加新的行為。

這邊強調的是使用繼承的合理性，因為繼承是依賴關係中最強的，若是換成子類別來操作則丟出 exception 就不是好的做法，不應該只是為了減少重複程式碼而使用繼承，真正的繼承是"這個子類別能不能做到父類別預期的行為"來作為準則，這也才是 Liskov 替換的核心原則

反例如下：企鵝繼承鳥類、正方形繼承矩形。這些會有什麼問題呢？
企鵝不會飛，但鳥會飛，若是企鵝繼承鳥的話，在 Fly() 必須另外處理，這些另外處理常常會造成程式不可預期的錯誤，同理正方形繼承矩形時，必須在設定長跟寬時特殊處理，才會在呼叫 GetArea() 時得到正確結果。

解決方法應為兩者(鳥/企鵝、矩形/正方形)都繼承一共同介面，也就是上下階層不互相依賴，轉而依賴介面，其道理與開放封閉原則一致。

----------

## Interface Segregation Principle (ISP)

**介面隔離原則**

> Clients should not be forced to depend on methods that they do not use.

不應強迫 client 實作一個它用不到的 method。我們常常在使用介面時把一堆 method 放入同一介面(太肥)，再把此介面提供給 A, B class 繼承使用，
但其中有些介面中的的 method 在 B class 中並不會實作，如 foo()，這時可能會在 B class foo() 內寫丟出 exception，但這種寫法明顯違反了"**Liskov 替換原則**"。
解決方法應為再定義另一介面，其內有 foo() method，B class 只繼承原有介面，而 A class 繼承原有介面以及含有 foo() 的介面。

這邊強調的觀念是: 設計介面時，也要單一職責，再依據實際的需求來用組合介面的方式來實現想要的功能

例如 C# 中的 List class 定義

~~~csharp
public class List<T> : IList<T>, ICollection<T>, IEnumerable<T>, IEnumerable, IList, ICollection, IReadOnlyList<T>, IReadOnlyCollection<T>
{

}
~~~

----------

## Dependency inversion principle (DIP)

**依賴反轉原則**

> High-level modules should not depend on low-level modules. Both should depend on abstractions.

高層模組不應該依賴底層模組，它們都應該依賴抽象。

> Abstractions should not depend on details. Details should depend on abstractions.

抽象不應該依賴細節。細節應該依賴抽象。

簡單來說就是 program to an interface, not an implementation.也就是透過介面來解耦。

~~~csharp
class Human
{
    void Eat(Apple apple)
    {
        // eat an apple
    }
}

class Apple
{
}

Apple apple = new Apple();
Human jack = new Human();
jack.Eat(apple);
~~~

現在需求改了，原來蘋果現在改成吃香蕉，難不成要改原有的程式嗎？

這時應該使用一個介面來隔開上下階層的關係，使得高層模組(Human)不應依賴低層模組(Apple)，應使他們依賴介面(Fruit)

~~~csharp
class Human
{
    void Eat(Fruit fruit)
    {
        // eat some fruit
    }
}

interface Fruit
{
}

class Apple:Fruit
{
}

class Banana:Fruit
{
}

Fruit fruit = new Banana();
Human jack = new Human();
jack.Eat(fruit);
~~~

仔細一看此修改符合了"**開放封閉原則**"與"**Liskov 替換原則**"，也為許多設計模式的重要概念基礎。