# 第二章 C# 核心

### C# 环境变量配置
添加环境变量pah
1. 查找对应文件夹在不在C:\Windows\Microsoft.NET\Framework\v4.0.30319
2. 将路径复制到path 里.

### 各种语言的开始! HelloWorld
边界 Frist.cs文件
```first
using System;

namespace Work{
    public class MyFirstClass{
        static void Main(){
            Console.WriteLine("hello world");
            Console.ReadLine();
            return;
        }
    }
}
```
编译文件<br>
```
文件路径下.
执行:csc First.cs
结果:生成 First.exe
```
运行文件<br>
结果:控制台输出 helloworld<br>