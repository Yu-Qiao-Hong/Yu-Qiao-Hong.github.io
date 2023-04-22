---
layout: post
title: C# Reflection
author: Yu-Qiao Hong
modified: 2016-06-23
tags: [C#]
---

定義 class：

~~~csharp
class Test
{
    // Property
    public string Name { get; set; }
    public int Age { get; set; }

    // Field
    public int Number;
    public double Length;

    // Method
    public void Print()
    {
        Console.WriteLine("Iverson Hong");
    }

    public void Print2()
    {
        Console.WriteLine("Hello world");
    }
}
~~~

透過反射取得物件的相關資訊：

~~~csharp
var type = typeof(Test);
Console.WriteLine("Class name: {0}", type.Name);

var properties = type.GetProperties();
foreach (var property in properties)
{
    Console.WriteLine("\tProperty: {0}, data type: {1}", property.Name, property.PropertyType.Name);
}

var fields = type.GetFields();
foreach (var field in fields)
{
    Console.WriteLine("\tField: {0}, data type: {1}", field.Name, field.FieldType.Name);
}

var methods = type.GetMethods();
foreach (var method in methods)
{
    if (method.IsSpecialName == false)
        Console.WriteLine("\tMethod: {0}, return data type: {1}", method.Name, method.ReturnType.Name);
}
~~~

結果：

    Class name: Test
        Property: Name, data type: String
        Property: Age, data type: Int32
        Field: Number, data type: Int32
        Field: Length, data type: Double
        Method: Print, return data type: Void
        Method: Print2, return data type: Void
        Method: ToString, return data type: String
        Method: Equals, return data type: Boolean
        Method: GetHashCode, return data type: Int32
        Method: GetType, return data type: Type

除了 Print(), Print2() 外其他 method 來自繼承的 object class