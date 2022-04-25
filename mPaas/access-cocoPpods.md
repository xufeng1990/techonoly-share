# 基于已有工程且使用 CocoaPods 接入

本文介绍如何基于 CocoaPods 原生的插件扩展机制生成各项配置，进而快速接入 mPaaS.

## 前置条件
- 已安装 CocoaPods 1.0.0 及以上版本，并确保要接入的工程是 CocoaPods 工程。
- 已安装 Cocoapods-mPaaS 插件。如您尚未安装该插件，可使用以下命令进行安装。

```shell
sh <(curl -s http://mpaas-ios.oss-cn-hangzhou.aliyuncs.com/cocoapods/installmPaaSCocoaPodsPlugin.sh)
```

- 已在控制台创建应用，并下载了 .config 配置文件。更多信息，参见 在控制台创建应用。

## 接入步骤
1. 将  **.config**  配置文件拷贝到工程的根目录下（与 Podfile 同级）。
> .config 配置文件是控制台下载的配置的文件：Ant-mpaas-ALIPUB5788924181041-default-iOS.config
2. 在命令行执行  **pod mpaas init**  命令，自动处理 Podfile 文件，并添加 plugin、source 以及 mPaaS_baseline 配置。
![输入图片说明](../images/pod%20mpaas%20init.png)

自动配置的代码如下：

```objc
 plugin "cocoapods-mPaaS"
 source "https://code.aliyun.com/mpaas-public/podspecs.git"
 mPaaS_baseline 'x.x.x' // 最新基线版本为 '10.1.68'
```
> 说明 需将代码中的 x.x.x 替换为实际的基线版本。
3. 配置 Podfile 文件。

   i. 指定 mPaaS 基线，修改 mPaaS_baseline。例如：mPaaS_baseline '10.1.68'，其中 10.1.68 为基线版本号，不同版本之间的区别，参见  **发布说明** 。

   ii. 添加 mPaaS 组件依赖，使用 mPaaS_pod。例如：mPaaS_pod "mPaaS_TinyApp"，其中 mPaaS_TinyApp 为组件名称，更多组件名称可参考下方的组件列表。

  组件配置|  适用基线 | 说明  |
|---|---|---|
mPaaS_pod "mPaaS_LocalLog"|  10.1.32+ |  本地日志 |
mPaaS_pod "mPaaS_Log"| 10.1.32+  | 移动分析：行为日志、自动化日志、Crash 日志、性能日志分析。  |   
mPaaS_pod "mPaaS_Diagnosis"|  10.1.32+ | 诊断：客户端诊断分析。  |  
mPaaS_pod "mPaaS_RPC"|  10.1.32+ |  移动网关：提供下载、上传、RPC 调用等功能。 |  
mPaaS_pod "mPaaS_Sync"| 10.1.32+  | 移动同步：长连接服务。  |  
mPaaS_pod "mPaaS_Push"| 10.1.32+  |  消息推送 |
mPaaS_pod "mPaaS_Config"|  10.1.32+ | 开关配置：根据 key 从服务端拉取对应的 value，可动态控制客户端逻辑。  |   
mPaaS_pod "mPaaS_Hotpatch"|  10.1.32+ |  热修复：动态修复（Hotpatch），用于在不发版的情况下热修复线上 Bug。 |  
mPaaS_pod "mPaaS_Upgrade"|   10.1.32+|  升级发布：提供便捷的主动检测升级的服务，可用于日常灰度发布、线上新版本更新提示。 | 
mPaaS_pod "mPaaS_Share" | 10.1.32+ | 分享：支持分享文本、图片到微博、钉钉、支付宝好友等知名渠道。
mPaaS_pod "mPaaS_Nebula" | 10.1.32+ | H5 容器与离线包：Nebula 容器，支持前端与 native 交互。
mPaaS_pod "mPaaS_UTDID" | 10.1.32+ | 设备标识：简单快捷地获取设备 ID，以利于应用程序安全有效的找到特定设备。
mPaaS_pod "mPaaS_DataCenter" | 10.1.32+ | 统一存储：提供安全、快速、可加密、支持多种数据类型的 KV 存储；数据库 DAO 支持等多种持久化方案。
mPaaS_pod "mPaaS_ScanCode" | 10.1.32+ | 扫码：快速识别二维码、条形码。
mPaaS_pod "mPaaS_LBS" | 10.1.32+ | 移动定位：移动客户端定位解决方案。
mPaaS_pod "mPaaS_CommonUI" | 10.1.32+ | 通用 UI：通用 UI 组件库，提供各种 UI 组件。
mPaaS_pod "mPaaS_BadgeService" | 10.1.32+ | 红点：客户端“红点”提醒组件，支持红点、数字、New 等提醒样式，自动管理树形结构的红点关系。
mPaaS_pod "mPaaS_AlipaySDK" | 10.1.32+ | 支付宝快捷支付：支付宝快捷收银台。
mPaaS_pod "mPaaS_Multimedia" | 10.1.32+ | 多媒体组件：多媒体组件，支持图片下载、上传、缓存等功能。
mPaaS_pod "mPaaS_MobileFramework" | 10.1.32+ | 移动框架：客户端应用框架，子 app 管理，多 tab 类应用管理，第三方跳转管理，viewController 跳转，异常处理与上报。
mPaaS_pod "mPaaS_OpenSSL" | 10.1.32+ | OpenSSL
mPaaS_pod "mPaaS_TinyApp" | 10.1.32+ | 小程序：小程序集成发布能力。
mPaaS_pod "MPBaseTest" | 10.1.32+ | 基础测试：基础的测试模块。
mPaaS_pod "mPaaS_CDP" | 10.1.32+ | 智能投放：智能配置各类营销广告和展示形式，动态投放到客户端。
mPaaS_pod "mPaaS_AliAccount" | 10.1.60+ | 小程序账户通：小程序账户通发布。
mPaaS_pod "mPaaS_ARTVC" | 10.1.68 | 音视频通话：音频、视频通话组件。支持双人、多人视频通话和在线会议。

4. 执行 pod mpaas update x.x.x，其中 x.x.x 为配置的基线号，例如 10.1.68
5. 执行 pod install 即可完成接入。您还可以追加 --verbose 查看详细日志。
6. 如果您在接入后遇到了三方库冲突，可将引起冲突的三方库移除。具体操作，请参见 [iOS 冲突处理](https://github.com/xufeng1990/techonoly-share/blob/master/mPaas/iosConflict.md)

## 升级指南
当 mPaaS 有新版本发布时，您可选择升级组件，或整体升级基线（即 SDK 版本）。
### 升级组件
1. 在命令行执行 pod mpaas update x.x.x，其中 x.x.x 为当前使用的基线版本号，例如 10.1.68
2. 执行 pod install 即可完成该基线下对应的组件的升级
### 升级基线
1. 在 podfile 中，修改 mPaaS_baseline 对应的基线号（支持标准或者定制基线），例如从 10.1.32 修改为 10.1.68，即可完成整体基线的升级。
2. 执行 pod install 即可完成基线升级。




