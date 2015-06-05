---
layout: post
title: "[翻译]Android M API 概览"
date: 2015-06-01 21:59:06 +0800
comments: true
categories: 
published: false
---
本文翻译自Android开发者网站[http://developer.android.com/preview/api-overview.html](http://developer.android.com/preview/api-overview.html)

## API概览
Android M开发者预览版让你能够抢先了解即将发行的Android平台。新版本为用户和应用开发者提供了新的功能。这篇文档介绍了最主要的几个API。

M开发者预览版主要针对**早期适配开发者**和**测试人员**。如果你想直接影响Android框架开发，[试一试Android M开发者预览版](http://developer.android.com/preview/setup-sdk.html)，然后把你的反馈发送给我们！

> **警告：**不要把使用M开发者预览版的应用发布到Play商城

---------------------

> **注意：**这篇文档有可能会引用到没有在[developer.android.com](http://developer.android.com/)的相关材料中涉及的类和方法。这些API元素会在文档中以`代码样式`标出。想要查看这些元素的早期文档，请下载[预览版参考文档](http://developer.android.com/preview/download.html#docs)

#### 重要行为变更

如果你以前曾经发布过Android app，请注意你的app可能会受平台相关的变化影响。    
请查看[行为变更](http://developer.android.com/preview/behavior-changes.html)获取完整信息。

### 应用链接
---
预览版通过应用链接增强了Android的Intent系统。这个功能允许将一个应用和你拥有的网络域名关联起来。基于这一关联，系统会选择默认打开一个特殊链接的应用，从而跳过弹出提示让用户选择的步骤。想了解如何实现这一功能，请查看[应用链接](http://developer.android.com/preview/features/app-linking.html)。

### 应用自动备份
---
系统现在提供自动地所有数据备份和恢复功能。这种行为在针对M开发的应用上默认为开启的；你并不需要添加额外的代码。如果用户删除了Google账号，那么他们的备份数据也同时被删除了。了解这一功能是如何工作的和如何配置文件系统上哪些是需要备份的，请查看[应用自动备份](http://developer.android.com/preview/backup/index.html)。

### 授权认证
---
预览版提供了新的API允许你在支持的设备上通过指纹来对用户进行授权认证，并且会检查用户最近一次使用的是哪种设备解锁机制（比如说锁屏密码）。查看[Android密钥系统](http://developer.android.com/training/articles/keystore.html)来集成使用这些API。

#### 指纹认证
通过扫描指纹的方式来认证用户，需要得到新增加的`android.hardware.fingerprint.FingerprintManager`类的实例，然后调用`FingerprintManager.authenticate()`方法。你的应用必须运行在拥有指纹传感器的兼容的设备上。在你的应用上你必须实现指纹认证流程的用户界面，并且必须使用标准的Android指纹图标。Android指纹图标（`c_fp_40px.png`）包含在[示例应用](https://github.com/googlesamples/android-FingerprintDialog)中。如果你同时开发多个使用指纹认证的应用，请注意每个应用都必须单独认证用户指纹。    
要在你的应用中使用这些功能，首先要在manifest中添加`USE_FINGERPRINT`权限。
 
```
<uses-permission
        android:name="android.permission.USE_FINGERPRINT" />
```

<div style="display: inline-block">
<div markdown="1" style="float:left; width:60%">
想要查看指纹认证的一个应用实现，请参考<a href="https://github.com/googlesamples/android-FingerprintDialog">指纹对话框示例</a>。

如果你要测试这些功能，下面就是需要的步骤：

<ol>
<li>如果你还没有安装Android SDK Tools 24.3版本，请先安装。</li>
<li>在<strong>设置>安全>指纹</strong>中登记一个新的指纹，根据登记的指引执行即可。</li>
<li>使用下面的命令来在一个模拟器上模拟指纹触摸事件。在解锁屏幕和你的应用中也是使用同样的命令来模拟指纹触摸事件。</li>
</ol>

```
adb -e emu finger touch <finger_id>
```

<p>在Windows上，你也许需要运行<code>telnet 127.0.0.1 <emulator-id></code>并在后面跟上<code>finger touch <finger_id></code>。</p>
</div>    
<div markdown="1" style="float:right; width:40%; text-align:right">
<img src="/images/fingerprint-screen.png"/>
</div>
</div>


#### 确认凭据
你的应用可以基于用户最近一次是如何解锁设备的来认证用户。这种功能将用户从不得不记住很多单独额外的应用密码中解放出来，并且可以避免你实现你自己的认证用户界面。而这种认证用户的功能需要同使用公钥或私钥认证的实现结合起来。

当创建**[KeyGenerator](http://developer.android.com/reference/javax/crypto/KeyGenerator.html)**或**[KeyPairGenerator](http://developer.android.com/reference/java/security/KeyPairGenerator.html)**时调用新的`android.security.keystore.KeyGenParameterSpec.setUserAuthenticationValidityDurationSeconds`方法来在用户成功认证后设置一个重用这个相同密钥的超时长。