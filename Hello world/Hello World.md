# Hello World
正如序言所说，每个人程序员写出的第一段代码都应该是通过控制台输出“Hello, World!”，这篇教程也不会例外。  
## 关于“Hello, World!”
1972年， Brian Kernighan在一本贝尔实验室的内部指南中第一次提到了“Hello, World!”字符串，其后在1978年出版的《The C Programming Language》一书中，他将控制台输出“Hello, World!”这一字符串作为了测试消息的示例程序。  

```C
main() {
        printf("hello, world");
}
```
这段代码使用的就是后来大名鼎鼎的C语言，main()表明了这个程序改从此处开始运行，printf是C语言内置函数，作用就是通过控制台打印内容，这里需要打印的是“hello, world”。后来，输出Hello World成为了程序员学习新的编程语言的传统。  
现如今，“Hello, World!”这个短语也随着计算机编程的普及，渐渐影响到了流行文化。  
## 尝试输出“Hello, World!”
这里将尽可能简短的介绍一下如何使用python3语言输出“Hello, World!”。  
1. 打开python官网，下载python3（本文编写时，python3最新版本为3.11.2，注：python3和python2为不同版本的python，语法略有区别）。官网：https://www.python.org/downloads/  
2. 打开python idle  
3. 输入`print("Hello, World!")`并回车  
## 其他各主流程序语言“Hello, World!”代码
C语言：  

```C
#include <stdio.h>
#include <stdlib.h>
int main(void)
{
    printf("Hello, World!\n");
    return EXIT_SUCCESS;
}
```

C++：  

```C++
#include <iostream>
int main()
{
  std::cout << "Hello, World!" << std::endl;
  return 0;
}
```

C#：  

```C#
using System;

namespace Program 
{
    class Program
    { 
        public static void Main(string[] args)
        {
            System.Console.WriteLine("Hello, World!");
        }
    }
}
```

Go：  

```Go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

Java：  

```Java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello World");
    }
}
```

JavaScript：  

```JavaScript
console.log("Hello World");
```


