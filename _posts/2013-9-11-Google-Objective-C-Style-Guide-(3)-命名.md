---
layout: default
---


##命名

命名规则在obc中非常的重要。obc的命名看上去一般都很长，但是正因为这样，代码可读性很好，也省了很多的注释

编写代码的时候，我们应该遵循[Objective C 命名规范](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html),这些命名规范很多都是从C++来的

任何class，category，method或者变量都应该采用驼峰命名法，即里面的单词首字母要大写，当然像URL，TIFF，EXIF这些除外

###文件名

文件名应该和内部代码统一

- .h 头文件
- .m Objective C实现文件
- .mm Objective C++实现文件
- .cc 纯C++实现文件
- .c C实现文件

###类名

类名，category，protocol首字母应该大写

应用级别的代码，不应该使用前缀，因为没有什么用处，而且损害可读性，但是共享代码则需要前缀来避免命名冲突

###Category名

category名应该以2或2个大写前缀开始，来表示这是哪个类的

###方法名

方法名一般是小写开始，命名规范遵循[Apple's Guid to Nameing Methods](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF)

    - (id)getDelegate;  // 避免
    - (id)delegate;    // GOOD

###变量名

变量名都以小写字母开头，类的变量以下划线开头：myLocalVaribale、_myInstanceVariable

命名不应该太简单，比如int x这种，应该尽量描述这个变量的用处，以便新人第一眼就可以读懂

下面是**不好**的命名：

    int w;
    int nerr;
    int nCompConns;
    tix = [[NSMutableArray alloc] init];
    obj = [someObject object];
    p = [network port];
    
下面的命名是OK的：

    int numErrors;
    int numCompletedConnections;
    tickets = [[NSMutableArray alloc] init];
    userInfo = [someObject object];
    port = [network port];

常量的命名（#define enums const）应该以一个小写字母k开始，例如：

    const int kNumberOfFiles = 12;
    NSString *const kUserKey = @"kUserKey";
    enum DisplayTinge {
      kDisplayTingeGreen = 1,
      kDisplayTingeBlue = 2
    };

因为Objective-C并没有命名空间，所以供全局使用的变量，应该加一些适当的前缀，比如kClassNameFoo

