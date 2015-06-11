---
layout: post
title: "[翻译]Android M API 概览"
date: 2015-06-01 21:59:06 +0800
comments: true
categories: 
published: true
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
<div style="float:left; width:60%">
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
<div style="float:right; width:40%; text-align:right">
<img src="/images/fingerprint-screen.png"/>
</div>
</div>


#### 确认凭据
你的应用可以基于用户最近一次是如何解锁设备的来认证用户。这种功能将用户从不得不记住很多单独额外的应用密码中解放出来，并且可以避免你实现你自己的认证用户界面。而这种认证用户的功能需要同使用公钥或私钥认证的实现结合起来。

当创建**[KeyGenerator](http://developer.android.com/reference/javax/crypto/KeyGenerator.html)**或**[KeyPairGenerator](http://developer.android.com/reference/java/security/KeyPairGenerator.html)**时调用新的`android.security.keystore.KeyGenParameterSpec.setUserAuthenticationValidityDurationSeconds`方法来在用户成功认证后设置一个重用这个相同密钥的超时时长。这个功能目前是使用的是对称加密操作。

为了避免频繁的显示重新认证对话框——你的应用应该在第一次和超时后使用加密对象，在你的应用中通过[createConfirmDeviceCredentialIntent()]("http://developer.android.com/reference/android/app/KeyguardManager.html#createConfirmDeviceCredentialIntent(java.lang.CharSequence, java.lang.CharSequence)")来重新认证用户。

想要查看这个功能的实现，请参考[确认凭据示例](https://github.com/googlesamples/android-ConfirmCredential)。

### 直接分享
---
<div style="display: inline-block">
<div style="float:left; width:60%">
<p>预览版提供了可以快速直接分享到用户的API。你可以定义<em>直接分享目标</em>来运行你应用中的指定Activity.直接分享目标通过<em>分享</em>菜单来呈现给用户。这一功能允许用户在其他应用中分享内容到指定目标，比如联系人。例如，直接分享目标可以启动一个其他社交应用中的Activity，通过这个应用可以让用户直接分享内容到指定用户和社团。</p>
<p>使用直接分享目标功能你需要定义一个继承自<code>android.service. 
chooser.ChooserTargetService</code>的类。在manifest中声明你的<code>ChooserTargetService</code>。在声明里面，还需要标明<code>BIND_CHOOSER_TARGET_SERVICE</code>权限和包含<code>SERVICE_INTERFACE</code>action的Intent Filter。</p>
</div>
<div style="float:right; width:40%; text-align:right">
<img src="/images/direct-share-screen.png"/>
</div>
</div>
下面的例子展示了你应该如何在你的manifest中声明`ChooserTargetService`。

```
<service android:name=".ChooserTargetService"
        android:label="@string/service_name"
        android:permission="android.permission.BIND_CHOOSER_TARGET_SERVICE">
    <intent-filter>
        <action android:name="android.service.chooser.ChooserTargetService" />
    </intent-filter>
</service>
```

在manifest中每一个你想要暴露给`ChooserTargetService`的Activity加上带有`android.service.chooser.chooser_target_service`名称的`<meta-data>`元素。

```
<activity android:name=".MyShareActivity”
        android:label="@string/share_activity_label">
    <intent-filter>
        <action android:name="android.intent.action.SEND" />
    </intent-filter>
<meta-data
        android:name="android.service.chooser.chooser_target_service"
        android:value=".ChooserTargetService" />
</activity>
```

### 语音交互
---
预览版提供了新的语音交互API，同[语音指令](https://developers.google.com/voice-actions/)一起，允许你在应用中构建会话式的语音体验。调用`android.app.Activity.isVoiceInteraction()`方法来确定你的Activity是否是被语音指令启动的。如果是的话，你的应用可以使用`ndroid.app.VoiceInteractor`类来向用户请求语音确认，从列表中选择选项以及其他。想了解更多语音指令的实现，请查看[语音指令开发者网站](https://developers.google.com/voice-actions/interaction/)

### 助手功能API
---
预览版给用户提供了一个新的方式，通过助手来同你的应用交互。要使用这一功能，用户必须允许助手使用当前的上下文。一旦允许后，用户可以在任意应用中通过长按**Home**键来唤起助手。

你的应用可以通过设置[FLAG_SECURE](http://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#FLAG_SECURE)标志来选择不向助手分享当前上下文。除了平台本身传递给助手的信息之外，你的应用还可以通过新的`android.app.Activity.AssistContent`类来分享额外信息。

要想分享你的应用中的额外信息，请跟随以下步骤：   
1. 实现[Application.OnProvideAssistDataListener](http://developer.android.com/reference/android/app/Application.OnProvideAssistDataListener.html)接口。   
2. 使用[registerOnProvideAssistDataListener()]("http://developer.android.com/reference/android/app/Application.html#registerOnProvideAssistDataListener(android.app.Application.OnProvideAssistDataListener)")注册这个接口。   
3. 为了提供基于上下文的活动行为，覆写[onProvideAssistData()]("http://developer.android.com/reference/android/app/Activity.html#onProvideAssistData(android.os.Bundle)")回掉调，或者可以选择使用新的`Activity.onProvideAssistContent()`回调。

### 通知
---
预览版添加了下列与通知相关的API更改：
 
- 新的`NotificationListenerService.INTERRUPTION_FILTER_ALARMS`过滤级别，用以同新的*只允许闹钟*的勿扰模式匹配。   
- 新的`Notification.CATEGORY_REMINDER`分类值，用于将用户计划的提醒同其他的提醒([CATEGORY_EVENT](http://developer.android.com/reference/android/app/Notification.html#CATEGORY_EVENT))和闹钟([CATEGORY_ALARM](http://developer.android.com/reference/android/app/Notification.html#CATEGORY_ALARM))区分开来.
- 新的`android.graphics.drawable.Icon`类，可以通过使用`Notification.Builder.setSmallIcon(Icon)`和`Notification.Builder.setLargeIcon(Icon)`方法添加到你的通知上去。   
- 新的`NotificationManager.getActiveNotifications()`方法，以允许你的应用可以可以找出哪些通知是处于活动状态的。想要查看使用了这一功能的应用实现，查看[活动通知示例](https://github.com/googlesamples/android-ActiveNotifications)。

### 蓝牙压感支持
---
预览版改进提升了对使用蓝牙压感输入的支持。用户可以将兼容的蓝牙压感设备同他们的手机或者平板配对连接。当连接后，从触摸屏获得的位置信息将混合压感设备的压力和按钮信息，以提供比单单使用触摸屏更大范围的表达方式。你的应用可以通过在Activity中注册新的`View.onStylusButtonPressListener`和`GestureDetector.OnStylusButtonPressListener`回调来监听压感按钮按下事件以出发次级行为。

使用[MotionEvent](http://developer.android.com/reference/android/view/MotionEvent.html)方法和常量来识别压感按钮交互：

* 如果用户使用压感笔来点击你应用屏幕上的按钮，[getTooltype()]("http://developer.android.com/reference/android/view/MotionEvent.html#getToolType(int)")方法将返回[TOOL_TYPE_STYLUS](http://developer.android.com/reference/android/view/MotionEvent.html#TOOL_TYPE_STYLUS)。
* 为Android M设计的应用，[getButtonState()]("http://developer.android.com/reference/android/view/MotionEvent.html#getButtonState()")方法在用户点击主压感按钮时会返回`MotionEvent.STYLUS_BUTTON_PRIMARY`，如果是一个次级压感按钮被用户按下，同一个方法会返回`MotionEvent.STYLUS_BUTTON_SECONDARY`。如果用户同时按下两级按钮，这个方法会返回两个值或运算后的结果（`STYLUS_BUTTON_PRIMARY|STYLUS_BUTTON_SECONDARY`）。
* 为低版本设计的应用，[getButtonState()]("http://developer.android.com/reference/android/view/MotionEvent.html#getButtonState()")方法返回[BUTTON_SECONDARY](http://developer.android.com/reference/android/view/MotionEvent.html#BUTTON_SECONDARY)(当主要压感按钮被按下)，[BUTTON_TERTIARY](http://developer.android.com/reference/android/view/MotionEvent.html#BUTTON_TERTIARY)(当次级压感按钮被按下)，或者同时返回两者。

### 提升蓝牙低功耗扫描