---
layout: post
title: C# Regular Expression
author: Yu-Qiao Hong
modified: 2016-04-30
tags: [C#]
---

## 正規表示式

寫程式常會遇到必須輸入特定規則的字串，否則視為不合法，這時就需要用到正規表示式。

若是使用得宜，可以減少非常大量的比對字串程式，以下為 C# Regular Expression 用法介紹：

---

## 語法規則

其規則眾多，但在絕大部分的程式語言中幾乎都通用，以下只列出經常用到的:

| **Pattern** | **Description**            |
|:------------|:---------------------------|
| ^           | At start of string or line |
|----
| $ | At end (or before \n at end) of string or line |
|----
| * | 0 or more times |
|----
| + | 1 or more times |
|----
| ? | 0 or 1 time |
|----
| {n} | Exactly n times |
|----
| {n,} | At least n times |
|----
| {n,m} | From n to m times |
|----
| [a-z] | In the a-z range |
|----
| [^a-z] | Not in the a-z range |
|----
| . | Any except \n (new line) |
|----
| \d | Decimal digit, equal to [0-9] |
|----
| \D | Not a decimal digit, equal to [^0-9] |
|----
| \w | Word character, equal to [A-Za-z0-9_] |
|----
| \W | Non-word character, equal to [^A-Za-z0-9_] |
|----
| \s | White-space character, equal to [\f\n\r\t\v] |
|----
| \S | Non-white-space char, equal to [^\f\n\r\t\v] |
|----
{: rules="groups"}

----------

## 程式寫法

~~~csharp
string str = "My name is Iverson Hong, my number is 3.";
Match match = Regex.Match(str, @"^My name is ([\w\s]+), my number is (\d+).$");
if (match.Success)
{
    string name = match.Groups[1].ToString(); // Iverson Hong
    string number = match.Groups[2].ToString(); // 3
}
~~~

---

## Reference ##

- [https://msdn.microsoft.com/zh-tw/library/az24scfc(v=vs.110).aspx](https://msdn.microsoft.com/zh-tw/library/az24scfc(v=vs.110).aspx)