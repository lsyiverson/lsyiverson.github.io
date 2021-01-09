---
layout: post
title: "Robolectric 3.0与Android SDK 23一起使用时的几个坑"
date: 2015-11-30 00:08:02 +0800
comments: true
tags: 
  - Robolectric
  - Android
  - UnitTest
categories: 
  - [Mobile]
  - [Android]
---

这两天在写一个简单地Android应用，准备顺手练练TDD。为了能够达到快速开发的需求，就选择了Robolectric这个测试框架来进行单元测试。之前已经在正式项目中用过Robolectric，本以为应该没有什么可能遇到的坑，结果却截然相反。

项目代码可以到[这里](https://github.com/lsyiverson/terminator)查看。因为是自己写着练手的应用，就准备把所有新的东西都用上，只管最新，不管有没有坑。刚开始搭的时候在`Travis CI`, `retrolambda`, `databinding`这些上面都没有遇到啥问题，我之前担心的retrolambda和Travis CI兼容的问题也并没有出现。直到用上了Robolectric，就发现下面几个东西与Robolectric 3.0一起用的话确实有些坑:

+ Android SDK 23
+ Android Design Support 23.1.1
+ 最新集成在Android Gradle Tools 1.5.0里面的databinding

下面具体讲讲都遇到了啥问题。

### 1. Robolectric 3.0目前并不支持Android SDK 22以上的版本

因为sdk中用于开发的android.jar是精简过的，只能保证编译时不出错，而在用于单元测试运行时都会抛出`java.lang.RuntimeException(“Stub!”)`异常。Robolectric就是为了解决这个问题而生，它完全模拟了Android SDK的jar文件，也正是这样，也就导致了每次Android版本升级后要等到Google放出源代码后，维护者才能去更新jar文件以支持所有新版Android系统的所有接口。Android Marshmallow的源代码刚刚放出没有多久，这也就导致Robolectric现在的版本没办法支持使用SDK 23开发的软件。不过好消息是，作者已经push了一个support-api-23的分支，应该不用多久就可以解决对api 23的支持。

### 2. Robolectric 3.0目前并没有对Design Support中widget的Shadow

我在项目中使用了一些Android Design Support中提供的Widget，比如说FloatingActionButton。但是Robolectric目前并没有对这些widget实现shadow，在运行测试的时候就会因为不能实际调用到真实的方法而产生错误。虽说shadow是可以自己去实现的，不过还是略显麻烦了，希望官方能够提供更多的widget的shadow来供使用。

### 3. Robolectric 3.0不能支持Gradle Tools 1.5中的Databinding使用方式

在之前的Gradle Tools版本中，databinding是以Gradle插件的形式提供的，Robolectric是能够支持databinding的使用的。不过Tools升级到1.5版本后，新的databinding是直接集成到一起的。目前的Robolectric还不能支持这样的使用方式。会直接提示找不到`android.view.layout`,这导致布局根本无法加载。这样的问题还是就只有等官方什么时候能够提供支持进行升级吧。

因为自己的项目在开发过程中遇到了这样的问题，便记录下来。不过这样给我提了一个醒，在真正的项目中用到的框架一定要先调查清楚是否兼容，升级SDK时也要考虑清楚是否与现有的第三方框架兼容，否则的话在项目中途遇到一些坑的话，代价就有点大了。
