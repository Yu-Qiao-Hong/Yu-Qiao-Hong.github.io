---
layout: post
title: C# 抓取檔案時間
author: Yu-Qiao Hong
modified: 2016-05-18
tags: [C#]
---

## C#抓取檔案時間

常見的時間為建立時間、修改時間以及存取時間

![]({{ "/images/CSharp_File_Get_Time/001.png" | relative_url}})

---

## Code:

~~~csharp
string path = @"C:\Users\user\Desktop\123.xml";
bool result = File.Exists(path);
if (result)
{
    FileInfo file = new FileInfo(path);
    Debug.WriteLine(file.CreationTime.ToString());
    Debug.WriteLine(file.LastWriteTime.ToString());
    Debug.WriteLine(file.LastAccessTime.ToString());
}
~~~

結果:

    2016/4/24 上午 08:54:03
    2016/5/18 下午 11:39:44
    2016/4/24 上午 08:54:03