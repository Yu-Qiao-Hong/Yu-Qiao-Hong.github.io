---
layout: post
title: C# Decorator Pattern
author: Yu-Qiao Hong
modified: 2019-11-03
tags: [C#, Design Pattern]
---

## 目的/使用時機

* 動態的對物件增加額外的職責，且不需修改既有的程式。

* 使用**合成**的方式動態增加職責，而非使用繼承(繼承是靜態的，且繼承相比合成有更高的耦合性)。

* 把一個禮物先裝在盒子裡，盒子外面再包一層包裝紙，再綁上絲帶 = 透過一層一層包裝(ConcreteDecorator)的方式把禮物(ConcreteComponent)包裝起來。

* 也算是一種 wrapper 的作法。

----------

## Participants

* Component: 用來定義動態附加的介面，給 wrappers 跟 被 wrap 的物件使用。

* ConcreteComponent: 要被 warp 的物件(可以被附加責任的物件)，可以先給予初始化的內容。

* Decorator: 裡面會有一個被 wrap 的物件。(一般是使用建構子將帶入 ConcreteComponent)

* ConcreteDecorator: 向 Component 添加職責的物件，再調用自己的 Method 前先調用父類別的。

----------

## UML

![]({{ "/images/CSharp_DesignPattern_Decorator/class-diagram.png" | relative_url}})

<!--
<div class="mermaid">
classDiagram

class Component{
    +Operation()
}

class ConcreteComponent{
    +Operation()
}

class Decorator{
    -_component: Component
    +Operation()
}

class ConcreteDecoratorA{
    +Operation()
}

class ConcreteDecoratorB{
    +Operation()
}

Component <|-- ConcreteComponent
Component <|-- Decorator
Component <--o  Decorator
Decorator <|-- ConcreteDecoratorA
Decorator <|-- ConcreteDecoratorB
</div>
-->

----------

## 優點

* 使用合成取代繼承，提供更多彈性。

* 具體類別(ConcreteComponent)與裝飾類別獨立設計，可依據需求動態增加裝飾的職責，且不需修改原有具體類別，符合開放封閉原則。

* 把類別的核心職責與裝飾功能區分開來，符合單一職責原則。

----------

## 缺點

* 若使用過多的裝飾類別會增加理解難度。

* 使用合成的方式增加職責(多次裝飾對象)，除錯時需逐步檢查，增加除錯難度。

* 若裝飾的順序會影響功能，則不建議使用，理想情況是裝飾類別彼此之間獨立，這樣就可以任意排列組合使用。

* 若要動態移除特定的裝飾功能較困難。

----------

## 範例

寫一漢堡類別，允許可動態地增加其多種配料，並計算出最後價錢。

### UML：

![]({{ "/images/CSharp_DesignPattern_Decorator/hambuger-class-diagram" | relative_url}})

<!--
<div class="mermaid">
classDiagram

class Hamburger{
    +Name: string
    +Price: int
}

class Cheeseburger{
    +Name: string
    +Price: int
}

class BigMac{
    +Name: string
    +Price: int
}

class BurgerDecoratorBase{
    -_hamburger: Hamburger
    +Name: string
    +Price: int
}

class BeefDecorator{
    +Name: string
    +Price: int
}

class LettuceDecorator{
    +Name: string
    +Price: int
}

class PicklesDecorator{
    +Name: string
    +Price: int
}

Hamburger <|-- Cheeseburger
Hamburger <|-- BigMac
Hamburger <|-- BurgerDecoratorBase
Hamburger <--o  BurgerDecoratorBase
BurgerDecoratorBase <|-- BeefDecorator
BurgerDecoratorBase <|-- LettuceDecorator
BurgerDecoratorBase <|-- PicklesDecorator
</div>
-->

### 定義一漢堡虛擬類別 (Component)：
~~~c#
/// <summary>
/// Component
/// </summary>
public abstract class Hamburger
{
    public abstract string Name { get; }

    public abstract int Price { get; }
}
~~~

### 定義兩個基本款漢堡：起司堡、大麥克 (ConcreteComponent)：
~~~c#
/// <summary>
/// ConcreteComponent
/// </summary>
public class Cheeseburger : Hamburger
{
    public override string Name
    {
        get => "Cheeseburger";
    }

    public override int Price
    {
        get => 80;
    }
}

/// <summary>
/// ConcreteComponent
/// </summary>
public class BigMac : Hamburger
{
    public override string Name
    {
        get => "BigMac";
    }

    public override int Price
    {
        get => 100;
    }
}
~~~

### 裝飾類別，繼承漢堡類別 (Decorator)：
~~~c#
/// <summary>
/// Decorator
/// </summary>
public abstract class BurgerDecoratorBase : Hamburger
{
    protected Hamburger _hamburger;

    public BurgerDecoratorBase(Hamburger hamburger)
    {
        _hamburger = hamburger;
    }
}
~~~

### 實際要加入漢堡的東西，繼承裝飾類別 (ConcreteDecorator)：
~~~c#
/// <summary>
/// ConcreteDecorator
/// </summary>
public class BeefDecorator : BurgerDecoratorBase
{
    public BeefDecorator(Hamburger hamburger) : base(hamburger)
    {
    }

    public override string Name
    {
        get => _hamburger.Name + ", add Beef";
    }

    public override int Price
    {
        get => _hamburger.Price + 20;
    }
}

/// <summary>
/// ConcreteDecorator
/// </summary>
public class LettuceDecorator : BurgerDecoratorBase
{
    public LettuceDecorator(Hamburger hamburger) : base(hamburger)
    {
    }

    public override string Name
    {
        get => _hamburger.Name + ", add Lettuce";
    }

    public override int Price
    {
        get => _hamburger.Price + 5;
    }
}

/// <summary>
/// ConcreteDecorator
/// </summary>
public class PicklesDecorator : BurgerDecoratorBase
{
    public PicklesDecorator(Hamburger hamburger) : base(hamburger)
    {
    }

    public override string Name
    {
        get => _hamburger.Name + ", add Pickles";
    }

    public override int Price
    {
        get => _hamburger.Price + 5;
    }
}
~~~

### client 端：
~~~c#
Hamburger burger1 = new Cheeseburger();     // +80
burger1 = new BeefDecorator(burger1);       // +20
burger1 = new LettuceDecorator(burger1);    // +5
burger1 = new PicklesDecorator(burger1);    // +5
burger1 = new BeefDecorator(burger1);       // +20

Console.WriteLine("burger1 Name: " + burger1.Name);     // burger1 Name: Cheeseburger, add Beef, add Lettuce, add Pickles, add Beef
Console.WriteLine("burger1 Price: " + burger1.Price);   // burger1 Price: 130
Console.WriteLine("======================================");

Hamburger burger2 = new BigMac();           // +100
burger2 = new LettuceDecorator(burger2);    // +5
burger2 = new PicklesDecorator(burger2);    // +5

Console.WriteLine("burger2 Name: " + burger2.Name);     // burger2 Name: BigMac, add Lettuce, add Pickles
Console.WriteLine("burger2 Price: " + burger2.Price);   // burger2 Price: 110
Console.WriteLine("======================================");
~~~

### 輸出結果：
```
burger1 Name: Cheeseburger, add Beef, add Lettuce, add Pickles, add Beef
burger1 Price: 130
======================================
burger2 Name: BigMac, add Lettuce, add Pickles
burger2 Price: 110
======================================
```