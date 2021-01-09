---
layout: post
title: "[翻译]Android M API 概览"
date: 2015-06-01 21:59:06 +0800
comments: true
tags: [Android, M-preview]
categories:
  - [Mobile]
  - [Android]
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
---
如果你的应用提供了蓝牙低功耗扫描，你可以使用新的`android.bluetooth.le.ScanSettings.Builder.setCallbackType()`方法来标明你只希望接收到的能够最先匹配设置了[ScanFilter](http://developer.android.com/reference/android/bluetooth/le/ScanFilter.html)的广播包，当这种包已经有一段时间没有接收到了。这一扫描方式比以前平台版本提供的更高效节能。

### 热点2.0第一版支持
---
预览版在Nexus 6和Nexus 9上提高了对热点2.0第一版规格的支持。使用[WifiEnterpriseConfig](http://developer.android.com/reference/android/net/wifi/WifiEnterpriseConfig.html)类的方法，例如`setPlmn()`和`setRealm()`来在你的应用中使用热点2.0的证书。在[WifiConfiguration](http://developer.android.com/reference/android/net/wifi/WifiConfiguration.html)对象中，你可以设置[FQDN](http://developer.android.com/reference/android/net/wifi/WifiConfiguration.html#FQDN)和`providerFriendlyName`的值。如果指定网络是热点2.0的接入点，新的`ScanResult.PasspointNetwork`属性会表明。

### 4K显示模式
---
安卓平台现在允许应用进行在兼容的平台显示分辨率提升到了4K渲染。使用新的`android.view.Display.Mode`的API来查询当前的物理分辨率。如果UI以低逻辑分辨率绘制但是放大到大尺寸物理分辨率显示时，请注意`Display.Mode.getPhysicalWidth()`方法返回值会跟[getSize()]("http://developer.android.com/reference/android/view/Display.html#getSize(android.graphics.Point)")报告的逻辑分辨率不一致。

你可以通过在你的应用窗口中设置`WindowManager.LayoutParams.preferredDisplayModeId`属性来在应用运行时请求系统更改物理分辨率。这个功能在你想切换到4K分辨率时非常有用。当处于4K模式时，UI会继续使用原始分辨率进行渲染（如1080P）然后放大到4K，但是[SurfaceView](http://developer.android.com/reference/android/view/SurfaceView.html)对象会按照本地分辨率来显示内容。

### 可设置主题的ColorStateLists
---
现在，运行M预览版的设备在[ColorStateList](http://developer.android.com/reference/android/content/res/ColorStateList.html)中已经支持主题属性。[getColorStateList()]("http://developer.android.com/reference/android/content/res/Resources.html#getColorStateList(int)")和[getColor()]("http://developer.android.com/reference/android/content/res/Resources.html#getColor(int)")方法已经被废弃了。如果你要调用这些API，调用新的`Context.getColorStateList()`或`Context.getColor()`来代替原来的方法。这些方法还可以在v4 appcompat库中通过[ContextCompat](http://developer.android.com/reference/android/support/v4/content/ContextCompat.html)调用。

### 视频功能
---
预览版为视频功能API加入了新的功能，包括以下几处：

* 新的`android.media.MediaSync`类用于帮助同步渲染音频与视频流。音频buffer会以非阻塞的方式通过回调进行返回。而且还支持动态播放率。
* 新的`MediaDrm.EVENT_SESSION_RECLAIMED`事件，它标明明应用打开的回话已经被资源管理器回收。如果你的应用使用了DRM回话，那么你必须处理这一事件来确保不要使用已经回收的回话。
* 新的`MediaCodec.CodecException.ERROR_RECLAIMED`错误码，它标明资源管理器回收了解码器使用的媒体资源。如果遭遇这一异常，解码器必须被释放，因为它已经处在中止状态。
* 新的`MediaCodecInfo.CodecCapabilities.getMaxSupportedInstances()`接口来获取解码器实例支持的最大并发数的相关提示。
* 新的`MediaPlayer.setPlaybackParams()`方法来设置快或者慢动作的媒体播放速率。它同时会自动加快或减慢音频的播放速率。

### 相机功能
---
预览版包含以下访问相机闪光灯和相机图像再处理的API：
#### 闪光灯API
如果相机设备包含闪光灯模组，你可以调用`CameraManager.setTorchMode()`方法来在不打开相机的情况下打开或关闭火炬模式。应用没有对闪光灯模组和相机设备的独占式请求。火炬模式将会在相机不可用时，或者在其他相机资源保持火炬模式变为不可用时关闭并且停用。其他应用也可以通过调用`setTorchMode()`来关闭火炬模式。当最后一个应用关闭火炬模式后，火炬模式将会关闭。

你可以通过调用`CameraManager.registerTorchCallback()`方法来注册火炬模式状态改变的回调。当回调第一次注册时，他会立即返回当前已知的所有包含闪光灯模组的相机设备。如果火炬模式成功的开启或关闭，`CameraManager.TorchCallback.onTorchModeChanged()`方法将会被调用。
#### 再处理API
[Camera2](http://developer.android.com/reference/android/hardware/camera2/package-summary.html) API扩展了对YUV和私有不透明格式图像的再处理。通过调用[getCameraCharacteristics()]("http://developer.android.com/reference/android/hardware/camera2/CameraManager.html#getCameraCharacteristics(java.lang.String)")来检查`REPROCESS_MAX_CAPTURE_STALL`关键字来判断是否具备再处理能力。如果设备支持再处理，你可以通过调用`CameraDevice.createReprocessableCaptureSession()`来创建一个可以再处理的相机捕获会话，并且创建输入缓冲再处理请求。

使用`android.media.ImageWriter`来连接输入缓冲流到相机再处理输入。下面是如果拿到一个空缓冲的编程模式：

1. 调用`ImageWriter.dequeueInputImage()`方法。
2. 将数据填入输入缓冲。
3. 通过调用`ImageWriter.queueInputImage()`方法将缓冲区发送到相机。

如果你将`ImageWriter`同一个`android.graphics.ImageFormat.PRIVATE`图片一起使用，你的应用将不能直接访问图像数据。取而代之的是通过不带缓冲拷贝的调用`ImageWriter.queueInputImage()`方法来直接传递`ImageFormat.PRIVATE`图像到`ImageWriter`.

[ImageReader](http://developer.android.com/reference/android/media/ImageReader.html)类现在支持`android.graphics.ImageFormat.PRIVATE`格式的图片流。这种支持允许你的应用维持一个[ImageReader](http://developer.android.com/reference/android/media/ImageReader.html)输出图像队列，选择一个或多个图像，然后发送到`ImageWriter`做相机再处理。

### Android for Work功能
---
预览版包含以下Android for Work的API：

* **对公司所有的单人使用设备的增强控制：**现在设备所有者可以控制一下设置来改进对公司所有单人使用的设备的管理（COSU)：
    * 通过[setKeyguardDisabled()]("http://developer.android.com/reference/android/app/admin/DevicePolicyManager.html#setKeyguardDisabled(android.content.ComponentName,%20boolean)")方法来禁用或启用键盘锁。"
    * 通过[setStatusBarDisabled()]("http://developer.android.com/reference/android/app/admin/DevicePolicyManager.html#setStatusBarDisabled(android.content.ComponentName,%20boolean)")方法来禁用或启用状态栏（包括快速设置、通知和滑动运用Google即时的手势)。
    * 通过[UserManager](http://developer.android.com/reference/android/os/UserManager.html)的常量[DISALLOW_SAFE_BOOT](http://developer.android.com/reference/android/os/UserManager.html#DISALLOW_SAFE_BOOT)来禁用或启用安全启动。
    * 使用[STAY_ON_WHILE_PLUGGED_IN](http://developer.android.com/reference/android/provider/Settings.Global.html#STAY_ON_WHILE_PLUGGED_IN)常量来防止屏幕在插入电源时关闭。

* **设备所有者可以静默安装和卸载应用：**设备所有者现在可以使用[PackageInstaller](http://developer.android.com/reference/android/content/pm/PackageInstaller.html)API来静默安装卸载应用，这是独立于Google Play for Work的。你可以作为设备所有者在没有用户交互的情况下来获取和安装应用完成设备准备。这一功能在一键准备或者设备没有激活Google账户的情况下非常有用。

* **静默的企业证书访问：**当一个应用调用[choosePrivateKeyAlias()]("http://developer.android.com/reference/android/security/KeyChain.html#choosePrivateKeyAlias(android.app.Activity,%20android.security.KeyChainAliasCallback,%20java.lang.String[],%20java.security.Principal[],%20java.lang.String,%20int,%20java.lang.String)")时，之前用户会被弹出提醒选择证书，现在个人或设备所有者可以调用[onChoosePrivateKeyAlias()]("http://developer.android.com/reference/android/app/admin/DeviceAdminReceiver.html#onChoosePrivateKeyAlias(android.content.Context,%20android.content.Intent,%20int,%20android.net.Uri,%20java.lang.String)")方法来向请求的应用程序静默地提供别名。这一功能让你能够在没有交互的情况下授权管理用户访问证书。

* **自动接受系统更新：**通过使用[setSystemUpdatePolicy()]("http://developer.android.com/reference/android/app/admin/DevicePolicyManager.html#setSystemUpdatePolicy(android.content.ComponentName,%20android.app.admin.SystemUpdatePolicy)")设置系统更新策略。设备所有者现在可以自动接受系统更新，例如以防有大量的设备或者推迟更新并防止用户在长达30天里应用更新。此外，管理员还可以设置时间窗口。例如在数小时内大量设备不会在使用的时间。当系统更新可用时，系统将会检查工作策略控制应用是否设置了系统更新策略，并依据此进行操作。

* **授权证书安装：**配置和设备所有者现在可以向第三方应用授予调用[DevicePolicyManager](http://developer.android.com/reference/android/app/admin/DevicePolicyManager.html)API来管理证书的能力：
    * [getInstalledCaCerts()]("http://developer.android.com/reference/android/app/admin/DevicePolicyManager.html#getInstalledCaCerts(android.content.ComponentName)")
    * [hasCaCertInstalled()]("http://developer.android.com/reference/android/app/admin/DevicePolicyManager.html#hasCaCertInstalled(android.content.ComponentName,%20byte[])")
    * [installCaCert()]("http://developer.android.com/reference/android/app/admin/DevicePolicyManager.html#installCaCert(android.content.ComponentName,%20byte[])")
    * [uninstallCaCert()]("http://developer.android.com/reference/android/app/admin/DevicePolicyManager.html#uninstallCaCert(android.content.ComponentName,%20byte[])")
    * [uninstallAllUserCaCerts()]("http://developer.android.com/reference/android/app/admin/DevicePolicyManager.html#uninstallAllUserCaCerts(android.content.ComponentName)")
    * [installKeyPair()]("http://developer.android.com/reference/android/app/admin/DevicePolicyManager.html#installKeyPair(android.content.ComponentName,%20java.security.PrivateKey,%20java.security.cert.Certificate,%20java.lang.String)")

* **数据使用跟踪：**配置和设备所有者现在可以通过新的[NetworkStatsManager](http://developer.android.com/reference/android/app/usage/NetworkStatsManager.html)方法来查询在**设置->数据**中可见的数据使用情况统计。配置所有者自动授权查询他们管理的配置上管理的数据，而设备所有者访问被管理的主要用户的数据使用情况。

* **运行时权限管理：**配置和权限管理员可以为所有使用了[setPermissionPolicy()]("http://developer.android.com/reference/android/app/admin/DevicePolicyManager.html#setPermissionPolicy(android.content.ComponentName,%20int)")设置了运行中请求的应用设置权限政策，而不是提醒用户授权或是静默地自动授权或拒绝授权。如果后者政策被设置了，用户将不能在**设置**中的应用权限屏幕更改由配置或设备管理员设定的选项。

* **在设置中的VPN：**VPN应用现在可以在**设置->更多->VPN**。此外，伴随VPN使用情况的通知消息现在还会指明VPN是如何配置的。对一个配置所有者，通知消息会表明VPN是否被一个已管理的配置文件、个人配置文件或者两者一起所配置。对一个设备所有者来说，通知消息会表明VPN是否是为整个设备所配置。

* **工作状态通知：**无论什么时候一个管理配置文件里面的应用在前台活动时都会出现一个公文包通知栏图标在状态栏。此外，如果一个设备是被一个管理配置文件中的应用活动直接解锁的话，一个toast消息将会通知用户他们处于工作配置中了。


| 想要查看所有M预览版中的API更改，请查阅[API差异报告](http://developer.android.com/preview/download.html)







