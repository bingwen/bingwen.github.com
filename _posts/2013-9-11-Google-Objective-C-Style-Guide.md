---
layout: default
---

背景
---
obc是一门继承于C的动态的，面向对象的开发语言。它本身就设计的特别容易阅读。这门语言本来是不温不火的，这两年由于ios的崛起，突然也变得时髦起来，总之，obc现在主要用来开发ios和mac os的应用。

Aplle自己本已经有了一套非常好的，并且完整的编码规范，但是Google自己也写了一份，以便于更加规范的开发obc程序，本文就是翻译[Google Object C Style-Guid](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)来的。

在阅读本文之前，你最好阅读过 (<del>没读过也没关系</del>)：

* [Apple的Cocoa编码规范](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [Google的C++编码规范](http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml)

反正在C++中的一些抵制的陋习，在编写obc的时候也是基本最好不好出现的

当然这篇文章也不是obi入门介绍，因此，如果你刚刚了解obi请先阅读[About Object C](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/Introduction/Introduction.html),如果已经有了一段开发经历，请继续阅读。

###例子
俗话说talk is cheap, show me the code，说的再多，还不如给一段代码示例更容易让大家理解，读者可以仔细品味下面这段代码的编码风格，比如空格、命名等。

首先是一段头文件，演示了声明一个@interface所用的必要注释和空格空行

    #import <Foundation/Foundation.h>
    
    // A sample class demonstrating good Objective-C style. All interfaces,
    // categories, and protocols (read: all top-level declarations in a header)
    // MUST be commented. Comments must also be adjacent to the object they're
    // documenting.
    //
    // (no blank line between this comment and the interface)
    @interface Foo : NSObject {
     @private
      NSString *_bar;
      NSString *_bam;
    }
    
    // Returns an autoreleased instance of Foo. See -initWithBar: for details
    // about |bar|.
    + (id)fooWithBar:(NSString *)bar;
    
    // Designated initializer. |bar| is a thing that represents a thing that
    // does a thing.
    - (id)initWithBar:(NSString *)bar;
    
    // Gets and sets |_bar|.
    - (NSString *)bar;
    - (void)setBar:(NSString *)bar;
    
    // Does some work with |blah| and returns YES if the work was completed
    // successfully, and NO otherwise.
    - (BOOL)doWorkWithBlah:(NSString *)blah;
    
    @end

再来一个源文件，演示了实现一个@implementation所需要的注释和空格空行，也包含了一些重要方法的实现，比如getters、setters、init、dealloc

    import "Foo.h"
    
    
    @implementation Foo
    
    + (id)fooWithBar:(NSString *)bar {
      return [[[self alloc] initWithBar:bar] autorelease];
    }
    
    // Must always override super's designated initializer.
    - (id)init {
      return [self initWithBar:nil];
    }
    
    - (id)initWithBar:(NSString *)bar {
      if ((self = [super init])) {
        _bar = [bar copy];
        _bam = [[NSString alloc] initWithFormat:@"hi %d", 3];
      }
      return self;
    }
    
    - (void)dealloc {
      [_bar release];
      [_bam release];
      [super dealloc];
    }
    
    - (NSString *)bar {
      return _bar;
    }
    
    - (void)setBar:(NSString *)bar {
      [_bar autorelease];
      _bar = [bar copy];
    }
    
    - (BOOL)doWorkWithBlah:(NSString *)blah {
      // ...
      return NO;
    }
    
    @end

@interface、@implementation、@end之前和之后的空行是可选的，无所谓，但是如果@interface里面声明了变量，那右大括号}之后是要空行的。

##空格空行和格式

###缩进

请使用空格（而不是tab）来缩进，缩进使用2个空格，如果使用tab，请配置好你的编辑器

###代码长度

一行代码最多80个字符,你可以在你的.vimrc里面设置下面的命令
    set cc=80
这样在vim中第80个字符的位置为一条红线来提醒你每次编码不超过这个数字

###方法的声明和定义

在-、+和返回类型之间应该有一个空格，参数列表之前没有空格，但是参数之间是需要空格的（换行最好），如下代码

一般的方法声明：

    - (void)doSomethingWithString:(NSString *)theString {
      ...
    }

*号之前空格是可有可无的

如果有许多的参数，能放到一行就放到一行，如果一行里面放不下，那么换行的时候应该使用冒号对齐的方式，如下

    - (void)doSomethingWith:(GTMFoo *)theFoo
                       rect:(NSRect)theRect
                   interval:(float)theInterval {
      ...
    }

如果第一个参数太他妈的短了，后面的参数又太长根本没法对齐呢？那第二个参数之前至少给4个空格就好了，后面仍坚持冒号对齐原则，如下

    - (void)short:(GTMFoo *)theFoo
              longKeyword:(NSRect)theRect
        evenLongerKeyword:(float)theInterval
                    error:(NSError **)theError {
      ...
    }

###方法调用

方法调用应该基本上和方法声明差不多

所有的参数在同一行

    [myObject doFooWith:arg1 name:arg2 error:arg3];

或者每个参数各占一行

    [myObject doFooWith:arg1
                   name:arg2
                  error:arg3];

请**别**像下面这样

    [myObject doFooWith:arg1 name:arg2  // 多于一个参数
                  error:arg3];
    
    [myObject doFooWith:arg1
                   name:arg2 error:arg3];
    
    [myObject doFooWith:arg1
              name:arg2  // 用单词对齐，应该使用冒号对齐
              error:arg3];

###@public和@private

@public和@private之前的缩进只要一个空格，基本上和C++中差不多

    @interface MyClass : NSObject {
     @public
      ...
     @private
      ...
    }
    @end

###异常

异常用到的@try、@catch、@finally应该单独一行，并且和大括号之间空格一个

如果你一定要抛异常，请按照下面的格式，当然，你可以看这里[为什么避免抛异常](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml#Avoid_Throwing_Exceptions)

    @try {
      foo();
    }
    @catch (NSException *ex) {
      bar(ex);
    }
    @finally {
      baz();
    }

###Protocols

protocol使用的时候在id和protocol之间不要空格，如下

    @interface MyProtocoledClass : NSObject<NSWindowDelegate> {
     @private
      id<MyFancyDelegate> _delegate;
    }
    - (void)setDelegate:(id<MyFancyDelegate>)aDelegate;
    @end

###Blocks

创建block的时候使用目标选择器模式，这样更容易阅读。block里面的代码缩进4个空格。

这里有几个规则，取决于这个block有多长：

- 如果一行可以放下，就不要嵌套了
- 如果必须嵌套，那么后面的大括号必须和声明起始的第一行的第一个字符对齐
- block内的代码必须缩进4个空格
- 如果一个block太他妈的长了，应该移除来，赋值给一个临时变量
- 如果block没有参数，'^'和'{'之间没有空格。如果有参数，'^'和'('之间没有空格，但是')'和'{'之间有空格

    // 一行可以放得下的情况
    [operation setCompletionBlock:^{ [self onOperationDone]; }];
    
    // 分行的，内部空4格，结束的}和第一行最左边对齐 
    [operation setCompletionBlock:^{
        [self.delegate newDataAvailable];
    }];
    
    // 使用C的API的时候，仍然是obc的对齐风格
    dispatch_async(_fileIOQueue, ^{
        NSString* path = [self sessionFilePath];
        if (path) {
          // ...
        }
    });
    
    // 有参数的情况，主意空格 |^(SessionWindow *window) {|
    [[SessionService sharedService]
        loadWindowWithCompletionBlock:^(SessionWindow *window) {
            if (window) {
              [self windowDidLoad:window];
            } else {
              [self errorLoadingWindow];
            }
        }];
    
    // 如果参数在一行放不下，换行，空格使用照旧 
    [[SessionService sharedService]
        loadWindowWithCompletionBlock:
            ^(SessionWindow *window) {
                if (window) {
                  [self windowDidLoad:window];
                } else {
                  [self errorLoadingWindow];
                }
            }];
    
    // block太长，声明一个临时变量 
    void (^largeBlock)(void) = ^{
        // ...
    };
    [_operationQueue addOperationWithBlock:largeBlock];
    
    // 一个方法里面多个block使用 
    [myObject doSomethingWith:arg1
        firstBlock:^(Foo *a) {
            // ...
        }
        secondBlock:^(Bar *b) {
            // ...
        }]; 

###容器和字符串

Xcode 4.4之后的项目，都推荐使用容器（array和dictionary）

如果一行可以放得下，在开始和结束的括号之间要有空格，如下

    NSArray* array = @[ [foo description], @"Another String", [bar description] ];
    
    NSDictionary* dict = @{ NSForegroundColorAttributeName : [NSColor redColor] };

而不是

    NSArray* array = @[[foo description], [bar description]];
    
    NSDictionary* dict = @{NSForegroundColorAttributeName: [NSColor redColor]};

如果一行里面放不下，那么分多行，内部需要空2格

这时候的dictionary里面冒号前后都要一个空格

    NSArray* array = @[
      @"This",
      @"is",
      @"an",
      @"array"
    ];
    
    NSDictionary* dictionary = @{
      NSFontAttributeName : [NSFont fontWithName:@"Helvetica-Bold" size:12],
      NSForegroundColorAttributeName : fontColor
    };

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

##注释


