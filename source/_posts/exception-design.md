
---
title: 异常处理设计
date: 2017-06-20 12:02:40
tags: 设计
categories: 设计
---

## 异常处理方法
参照[Oracle Java文档](https://docs.oracle.com/javase/tutorial/essential/exceptions/definition.html)对异常的定义
> Definition: 
An exception is an event, which occurs during the execution of a program, that disrupts the normal flow of the program's instructions.

### try/catch
程序要健壮就有必要设计合理的报错和异常处理机制，对于Java和C#的开发者常常使用try/catch来处理异常，但很多语言并没有这种机制，比如Perl、Lua、[Go](https://stackoverflow.com/questions/7198037/exception-handling-in-google-go-language)、C等等（Swift 2.0有了[do/catch](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/ErrorHandling.html)），要注意的是并不代表没有try/catch就等同于没有异常处理。
try/catch是一种“易上手但难用好”的机制，而且各种语言对其实现方式也是不尽相同。比如，许多C++开发文档不推荐使用try/catch机制来捕获处理异常，在[C++异常处理 知乎](https://www.zhihu.com/question/22889420)中的讨论阐述一种观点，C++的try/catch机制存在设计缺陷，异常还要保证异常安全，需要严格的[RAII](https://zh.wikipedia.org/wiki/RAII)机制配合才能写出异常安全的代码。而在[Google C++ Style](https://google.github.io/styleguide/cppguide.html#Exceptions)则是干脆不允许使用这种异常处理机制。讨论中还有一种说法是像C/C++这种没有GC的语言，都不应该使用try/catch机制来处理异常，这个观点值得商榷。
再比如，在一些语言中，开发者们严禁try/catch语法参与流程控制的编写，但在Python中，不少开发人员会让try/catch兼职if-else的功能，这也与Python对于try的设计有关。[python-better-to-try stackoverflow](https://stackoverflow.c## 异常处理方法
参照[Oracle Java文档](https://docs.oracle.com/javase/tutorial/essential/exceptions/definition.html)对异常的定义
> Definition: 
An exception is an event, which occurs during the execution of a program, that disrupts the normal flow of the program's instructions.

### try/catch
程序要健壮就有必要设计合理的报错和异常处理机制，对于Java和C#的开发者常常使用try/catch来处理异常，但很多语言并没有这种机制，比如Perl、Lua、[Go](https://stackoverflow.com/questions/7198037/exception-handling-in-google-go-language)、C等等（Swift 2.0有了[do/catch](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/ErrorHandling.html)），要注意的是并不代表没有try/catch就等同于没有异常处理。
try/catch是一种“易上手但难用好”的机制，而且各种语言对其实现方式也是不尽相同。比如，许多C++开发文档不推荐使用try/catch机制来捕获处理异常，在[C++异常处理 知乎](https://www.zhihu.com/question/22889420)中的讨论阐述一种观点，C++的try/catch机制存在设计缺陷，异常还要保证异常安全，需要严格的[RAII](https://zh.wikipedia.org/wiki/RAII)机制配合才能写出异常安全的代码。而在[Google C++ Style](https://google.github.io/styleguide/cppguide.html#Exceptions)则是干脆不允许使用这种异常处理机制。讨论中还有一种说法是像C/C++这种没有GC的语言，都不应该使用try/catch机制来处理异常，这个观点值得商榷。
再比如，在一些语言中，开发者们严禁try/catch语法参与流程控制的编写，但在Python中，不少开发人员会让try/catch兼职if-else的功能，这也与Python对于try的设计有关。[python-better-to-try stackoverflow](https://stackoverflow.com/questions/7604636/better-to-try-something-and-catch-the-exception-or-test-if-its-possible-first)

### 实际生产
由于各个语言对于异常处理方法有着很大的不同，这样在实际生产中，就要确定如何合理处理异常，团队整体是否能驾驭住对应语言try/catch的异常处理方式。通过对团队整体和语言的认识，才能合理有效的完成软件开发中，产品对于异常处理的设计。
> 对开发语言异常处理的认识和设定
> 团队整体对于异常处理的理解

## 延迟捕获异常
> 底层遇到的异常，如果是同层次不能处理的问题，应大胆抛到上层，让调用接口者通过业务需求选择合理的处理方式。

在产品开发场景中，为了避免产品Crash惹恼用户，开发者常常会选择在多处不同层次上捕获住异常，并且在低层级的接口捕获住异常之后没有再次throw出来，这种过度“关心”接口使用者的设计，会导致异常被默默隐藏掉，上层接口调用者由于无法得到正确的反馈不能进一步处理问题（大部分情况，接口调用的上层更清楚如何处理异常），而表现层也无法将应该用户了解的信息传达给用户。
笔者遇到的实际情况，多数并非“关心”所致，而是团队对异常的理解各有不同，使用方法也是各自为政，难以统一。更有开发者以代码冗余为由，在代码底层写try/catch减少冗余代码，但笔者认为
> 设计完整性的重要程度远高于冗余代码。

另一方面，在接口的使用中，调用接口者更知道如何正确处理不同的异常类型，而在无法正确处理的地方捕获，并使用日志这种较弱提示方式替代，不仅会导致问题无法被正确的方式解决，还会隐藏或延后实际问题被捕获时间，这种鸵鸟式的修补方式，等同于降低问题级别，更糟糕的情况则会导致异常扩散开来，无从查找问题的真正原因。
正确的解决方法应该像Java的[Fail-fast](https://en.wikipedia.org/wiki/Fail-fast)机制一样，尽早将问题抛出，强制开发者解决，才能从根本上处理掉问题。

### 实际生产
随着民用应用开发的普及，市场需求、窗口期等等的限制，敏捷开发在民用领域软件的地位正在逐步替代传统软件开发模式，软件开发迭代和需求的变化速度与日俱增，导致软件结构的设计无法被软件开发人员完全掌控，产品逻辑混乱，软件框架结构[腐化](http://www.cnblogs.com/rinson/p/6829966.html)，此时，通过Fast-Fail快速定位问题并处理自然是好的方法，重构框架也可以解决腐化问题，只是产品是否能够给予开发者足够开发时间。如果不能，那么，在底层捕获住异常来最低程度的保证程序“正常”执行下去，是否真的要全盘否定？而且，综合用户和软件自身的定位，并不是所有软件对于程序的鲁棒性都有着极高的要求，所以，很难说这种方式就是完全不可行的。
> 产品鲁棒性的要求
> 现有软件的框架和结构是否能被开发者完全掌控

另外，是否所有的Crash都是不能接受的，也值得讨论。如果是一些无法处理的系统异常错误，笔者认为最好老老实实的让程序抛出异常，收集dump信息。
> 一般情况下，只应捕获你知道如何从其恢复的异常。[C# try-catch ](https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/try-catch)

比如，网络的断线重连，可以捕获住进行重试。如果是内存不足，在很多场景中无法恢复现场的时候，最好直接抛出异常收集dump。

## 异常信息追溯与处理
Java的Checked Exception机制要求必须在函数头部做声明和检查，很多语言都有异常处理机制，但很少有要求在函数的类型里声明可能出现的异常类型，因为，这样开发起来确实很繁琐，承接Java，Android开发中的后来者Kotlin，在[文档](https://kotlinlang.org/docs/reference/exceptions.html#checked-exceptions)中也指出CE机制非常的繁琐，所以，在Kotlin中抛弃了CE。但在王垠的[Kotlin和Checked Exception](http://www.yinwang.org/blog-cn/2017/05/23/kotlin)文章中指出了CE的优越性，笔者也深有体会。例如，文中的例子，在C#中我们经常会看到这样的代码：
```CSharp
try
{
    foo();
}
catch(Exception)
{
    ...
}
```
由于缺少强制CE的支持，对于缺少注解的foo来说，很难清楚foo会报出什么异常，会不会抛出异常。所以在使用时都不得不套一层catch(Exception)。
foo缺少明确的异常信息，那么使用foo的函数也就同样难以写出明确的异常，这样的开发环境下，很难写出健壮的接口出来。为了解决这样的问题Resharp提供了[C# Exceptional](https://github.com/CSharpAnalyzers/ExceptionalReSharper)，可以通过这个工具来检查分析静态代码的异常信息，但是约束效果可见一斑。

### 实际生产
CE作为一种工程约束，可以很好的提高接口开发质量，但是，就如Kotlin文档中指出的，所有函数的定义都要完整的写出所要抛出的异常，确实非常繁琐，维护成本也很高，所以，个人认为并非所有的函数都需要这样繁琐的工作，对于一些类型的应用来说。哪些函数需要详细的异常信息，哪些不需要，就要具体情况具体分析，还有团队的默契程度了。CE是否是一种矫枉过正的约束这就仁者见仁了。

### throw
throw常常是容易被忽视的一个命令。当异常返回给上一层的信息时，原生异常结构无法满足明确异常信息的需求，就需要自定义异常，给出更饱满的信息。比如，当方法参数从调用方传递给其他库方法时，库方法引发的异常必须传递给调用方。
而throw不仅可以提供更详尽的信息，也可以隐藏一些不需要调用者了解的信息，比如数据库访问报出的异常，可以从新实例化一个新的异常传递给调用者。

### 多线程中的异常捕获
> 线程内部抛出的异常应该尽可能的在线程内部处理

Java基于JVM的设计理念：“线程是独立执行的代码片断，线程的问题应该由线程自己来解决，而不要委托到外部。”，当Java非主线程抛出异常时，不得不借助
```
// Java
setUncaughtExceptionHandler(Thread.UncaughtExceptionHandler eh)
```
来收集Unchecked Exception，其他线程不会有感知，程序也不会因此而终止。
但是C++与C#则不同，如果，一旦线程出现异常未被捕获，则会很快终止程序。
```
// C# 采集异常接口
Application.ThreadException or AppDomain.UnhandledException 
```

## 总结
本文关于异常设计的原则如下：
> 即时抛出异常防止异常扩散
> 延迟捕获异常
> 明确应该捕获的异常与层级

但是，无论任何设计原则都不能跨越团队整体的情况、时间节点和产品的定义，统一性远高于设计原则，因此，在实际团队开发的过程中，还应该考虑如下：

> 具体语言捕获异常处理方法的确认
> 团队异常处理规范统一
> 产品鲁棒性要求

## 参考
(1) [C++异常处理 知乎](https://www.zhihu.com/question/22889420)
(2) [Google C++ Style](https://google.github.io/styleguide/cppguide.html#Exceptions)
(3) [do-not-catch blog](http://blog.gauffin.org/2010/11/do-not-catch-that-exception/)
(4) [Swift为什么没有异常 知乎](https://www.zhihu.com/question/24261295)
(5) [ieee-fail-fast](https://martinfowler.com/ieeeSoftware/failFast.pdf)
(6) [Kotlin和Checked Exception 王垠](http://www.yinwang.org/blog-cn/2017/05/23/kotlin)
(7) [exception defintion](https://docs.oracle.com/javase/tutorial/essential/exceptions/definition.html)om/questions/7604636/better-to-try-something-and-catch-the-exception-or-test-if-its-possible-first)

### 实际生产
由于各个语言对于异常处理方法有着很大的不同，这样在实际生产中，就要确定如何合理处理异常，团队整体是否能驾驭住对应语言try/catch的异常处理方式。通过对团队整体和语言的认识，才能合理有效的完成软件开发中，产品对于异常处理的设计。
> 对开发语言异常处理的认识和设定
> 团队整体对于异常处理的理解

## 延迟捕获异常
> 底层遇到的异常，如果是同层次不能处理的问题，应大胆抛到上层，让调用接口者通过业务需求选择合理的处理方式。

在产品开发场景中，为了避免产品Crash惹恼用户，开发者常常会选择在多处不同层次上捕获住异常，并且在低层级的接口捕获住异常之后没有再次throw出来，这种过度“关心”接口使用者的设计，会导致异常被默默隐藏掉，上层接口调用者由于无法得到正确的反馈不能进一步处理问题（大部分情况，接口调用的上层更清楚如何处理异常），而表现层也无法将应该用户了解的信息传达给用户。
笔者遇到的实际情况，多数并非“关心”所致，而是团队对异常的理解各有不同，使用方法也是各自为政，难以统一。更有开发者以代码冗余为由，在代码底层写try/catch减少冗余代码，但笔者认为
> 设计完整性的重要程度远高于冗余代码。

另一方面，在接口的使用中，调用接口者更知道如何正确处理不同的异常类型，而在无法正确处理的地方捕获，并使用日志这种较弱提示方式替代，不仅会导致问题无法被正确的方式解决，还会隐藏或延后实际问题被捕获时间，这种鸵鸟式的修补方式，等同于降低问题级别，更糟糕的情况则会导致异常扩散开来，无从查找问题的真正原因。
正确的解决方法应该像Java的[Fail-fast](https://en.wikipedia.org/wiki/Fail-fast)机制一样，尽早将问题抛出，强制开发者解决，才能从根本上处理掉问题。

### 实际生产
随着民用应用开发的普及，市场需求、窗口期等等的限制，敏捷开发在民用领域软件的地位正在逐步替代传统软件开发模式，软件开发迭代和需求的变化速度与日俱增，导致软件结构的设计无法被软件开发人员完全掌控，产品逻辑混乱，软件框架结构[腐化](http://www.cnblogs.com/rinson/p/6829966.html)，此时，通过Fast-Fail快速定位问题并处理自然是好的方法，重构框架也可以解决腐化问题，只是产品是否能够给予开发者足够开发时间。如果不能，那么，在底层捕获住异常来最低程度的保证程序“正常”执行下去，是否真的要全盘否定？而且，综合用户和软件自身的定位，并不是所有软件对于程序的鲁棒性都有着极高的要求，所以，很难说这种方式就是完全不可行的。
> 产品鲁棒性的要求
> 现有软件的框架和结构是否能被开发者完全掌控

另外，是否所有的Crash都是不能接受的，也值得讨论。如果是一些无法处理的系统异常错误，笔者认为最好老老实实的让程序抛出异常，收集dump信息。
> 一般情况下，只应捕获你知道如何从其恢复的异常。[C# try-catch ](https://docs.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/try-catch)

比如，网络的断线重连，可以捕获住进行重试。如果是内存不足，在很多场景中无法恢复现场的时候，最好直接抛出异常收集dump。

## 异常信息追溯与处理
Java的Checked Exception机制要求必须在函数头部做声明和检查，很多语言都有异常处理机制，但很少有要求在函数的类型里声明可能出现的异常类型，因为，这样开发起来确实很繁琐，承接Java，Android开发中的后来者Kotlin，在[文档](https://kotlinlang.org/docs/reference/exceptions.html#checked-exceptions)中也指出CE机制非常的繁琐，所以，在Kotlin中抛弃了CE。但在王垠的[Kotlin和Checked Exception](http://www.yinwang.org/blog-cn/2017/05/23/kotlin)文章中指出了CE的优越性，笔者也深有体会。例如，文中的例子，在C#中我们经常会看到这样的代码：
```CSharp
try
{
    foo();
}
catch(Exception)
{
    ...
}
```
由于缺少强制CE的支持，对于缺少注解的foo来说，很难清楚foo会报出什么异常，会不会抛出异常。所以在使用时都不得不套一层catch(Exception)。
foo缺少明确的异常信息，那么使用foo的函数也就同样难以写出明确的异常，这样的开发环境下，很难写出健壮的接口出来。为了解决这样的问题Resharp提供了[C# Exceptional](https://github.com/CSharpAnalyzers/ExceptionalReSharper)，可以通过这个工具来检查分析静态代码的异常信息，但是约束效果可见一斑。

### 实际生产
CE作为一种工程约束，可以很好的提高接口开发质量，但是，就如Kotlin文档中指出的，所有函数的定义都要完整的写出所要抛出的异常，确实非常繁琐，维护成本也很高，所以，个人认为并非所有的函数都需要这样繁琐的工作，对于一些类型的应用来说。哪些函数需要详细的异常信息，哪些不需要，就要具体情况具体分析，还有团队的默契程度了。CE是否是一种矫枉过正的约束这就仁者见仁了。

### throw
throw常常是容易被忽视的一个命令。当异常返回给上一层的信息时，原生异常结构无法满足明确异常信息的需求，就需要自定义异常，给出更饱满的信息。比如，当方法参数从调用方传递给其他库方法时，库方法引发的异常必须传递给调用方。
而throw不仅可以提供更详尽的信息，也可以隐藏一些不需要调用者了解的信息，比如数据库访问报出的异常，可以从新实例化一个新的异常传递给调用者。

### 多线程中的异常捕获
> 线程内部抛出的异常应该尽可能的在线程内部处理

Java基于JVM的设计理念：“线程是独立执行的代码片断，线程的问题应该由线程自己来解决，而不要委托到外部。”，当Java非主线程抛出异常时，不得不借助
```
// Java
setUncaughtExceptionHandler(Thread.UncaughtExceptionHandler eh)
```
来收集Unchecked Exception，其他线程不会有感知，程序也不会因此而终止。
但是C++与C#则不同，如果，一旦线程出现异常未被捕获，则会很快终止程序。
```
// C# 采集异常接口
Application.ThreadException or AppDomain.UnhandledException 
```

## 总结
本文关于异常设计的原则如下：
> 即时抛出异常防止异常扩散
> 延迟捕获异常
> 明确应该捕获的异常与层级

但是，无论任何设计原则都不能跨越团队整体的情况、时间节点和产品的定义，统一性远高于设计原则，因此，在实际团队开发的过程中，还应该考虑如下：

> 具体语言捕获异常处理方法的确认
> 团队异常处理规范统一
> 产品鲁棒性要求

## 参考
(1) [C++异常处理 知乎](https://www.zhihu.com/question/22889420)
(2) [Google C++ Style](https://google.github.io/styleguide/cppguide.html#Exceptions)
(3) [do-not-catch blog](http://blog.gauffin.org/2010/11/do-not-catch-that-exception/)
(4) [Swift为什么没有异常 知乎](https://www.zhihu.com/question/24261295)
(5) [ieee-fail-fast](https://martinfowler.com/ieeeSoftware/failFast.pdf)
(6) [Kotlin和Checked Exception 王垠](http://www.yinwang.org/blog-cn/2017/05/23/kotlin)
(7) [exception defintion](https://docs.oracle.com/javase/tutorial/essential/exceptions/definition.html)
