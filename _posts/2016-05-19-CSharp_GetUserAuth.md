---
layout: post
title: C# 取得是否有使用者權限
author: Yu-Qiao Hong
modified: 2016-05-19
tags: [C#]
---

取得執行程式時是否有 Admin 權限:

## Code:

~~~csharp
private bool CheckIsAdmin()
{
    WindowsIdentity identity = WindowsIdentity.GetCurrent();
    WindowsPrincipal principal = new WindowsPrincipal(identity);
    bool isAdmin = principal.IsInRole(WindowsBuiltInRole.Administrator);
    return isAdmin;
}
~~~