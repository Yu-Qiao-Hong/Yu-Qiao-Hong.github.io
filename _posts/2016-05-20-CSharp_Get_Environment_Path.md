---
layout: post
title: "C# 取得系統環境路徑"
author: Yu-Qiao Hong
modified: 2016-05-20
tags: [C#]
---

在 C++ 程式中可用 SHGetFolderPath() 取得系統環境路徑，換到 C#，除了可用 import dll 方式之外，還可用 C# 提供的方法。

## Code:

~~~csharp
string str = Environment.GetFolderPath(Environment.SpecialFolder.Desktop); // C:\Users\user\Desktop
str = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments); // C:\Users\user\Documents
str = Environment.GetFolderPath(Environment.SpecialFolder.ProgramFiles); // C:\Program Files (x86)
str = Environment.GetFolderPath(Environment.SpecialFolder.ApplicationData); // C:\Users\user\AppData\Roaming
~~~

----------

## Reference

- [https://msdn.microsoft.com/zh-tw/library/system.environment.specialfolder(v=vs.100).aspx](https://msdn.microsoft.com/zh-tw/library/system.environment.specialfolder(v=vs.100).aspx)
- [https://msdn.microsoft.com/zh-tw/library/bb762494.aspx](https://msdn.microsoft.com/zh-tw/library/bb762494.aspx)