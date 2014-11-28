---
layout: post
title: "Google Objective-C Style Guide (1) 背景"
date: 2013-09-11 10:29:51
category: "Google-Objective-C-Style-Guide"
tags: google obj-c
author: bingwen
---

背景
---
obc是一门继承于C的动态的，面向对象的开发语言。它本身就设计的特别容易阅读。这门语言本来是不温不火的，这两年由于ios的崛起，突然也变得时髦起来，总之，obc现在主要用来开发ios和mac os的应用。

Aplle自己本已经有了一套非常好的，并且完整的编码规范，但是Google自己也写了一份，以便于更加规范的开发obc程序，本文就是翻译[Google Object C Style-Guid](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)来的。

<!--break-->
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



