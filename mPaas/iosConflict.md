# iOS 冲突处理
接入 mPaaS 时，mPaaS SDK 可能会和工程中引入的其他开源库或三方库发生冲突，导致工程编译不通过。根据引起冲突的库的类型，可以将解决方案分为以下两类：
- mPaaS 定制库：若发生冲突的 mPaaS SDK 为定制库，则必须使用这些 mPaaS 库。
- 非 mPaaS 定制库：若发生冲突的 mPaaS SDK 非 mPaaS 定制库，可以将 mPaaS 引入的库进行删除。

## mPaaS 定制库冲突解决方案
若发生冲突的 mPaaS SDK 为定制库，则必须使用这些 mPaaS 库。
| 开源库名  | mPaaS 库名  |  冲突解决方案 |
|---|---|---|
| AlipaySDK  | AlipaySDK  | 必须使用 mPaaS 版本（解决了与 mPaaS RPC、UTDID 等模块的冲突  |
| OpenSSL  | APOpenSSL  |  必须使用 mPaaS 版本（对原有国密算法进行优化）。更多详细信息，请参见 如何解决 iOS 工程中的 OpenSSL 三方库冲突 。 |
| protocolBuffers  | APProtocolBuffers  |  必须使用 mPaaS 版本 |

## 非 mPaaS 定制库冲突解决方案

若发生冲突的 mPaaS SDK 非 mPaaS 定制库，可以将 mPaaS 引入的库进行删除，支持删除的库如下表所示。详情请参见 移除冲突的三方库 移除引起冲突的库。

|  remove_pod 支持的组件 |  包含的开源库 |
|---|---|
|  mPaaS_SDWebImage | SDWebImage  |
|  mPaaS_Masonry | Masonry  |
| mPaaS_MBProgressHud  | MBProgressHUD  |
| mPaaS_TTTAttributedLabel  | TTTAttributedLabel  |
| mPaaS_Lottie  |  Lottie |
| mPaaS_AMap  | AMapSearchKit AMapFoundationKit MAMapKit |
|  mPaaS_Security |  SecurityGuard SGMain |
| mPaaS_APWebP  |  WebP |

### 移除冲突的三方库

若发生冲突的 mPaaS SDK 非 mPaaS 定制库，可参照以下步骤删除 mPaaS 引入的库。

#### 操作步骤
1. 安装 beta 版 cocoapods-mPaaS 插件

   **说明** ：cocoapods-mPaaS 插件 beta 版仅支持在 10.1.68 基线中使用。


```shell
sh <(curl -s http://mpaas-ios-test.oss-cn-hangzhou.aliyuncs.com/cocoapods/installmPaaSCocoaPodsPlugin.sh)
```
安装完成后，使用命令 pod mpaas version --plugin 确认是 beta 版本。

```shell
pod mpaasversion --plugin
0.9.5.0.0.7-beta
```

2. 重新运行命令更新本地基线：pod mpaas update 10.1.68
3. 使用 mPaaS_pod 命令之前，在 podfile 里引入 remove_pod "mPaaS_xxx"。

  >  比如，在 mPaaS_pod "mPaaS_CommonUI" 之前使用 remove_pod "mPaaS_SDWebImage" 去除 SDWebImage。

```objc
remove_pod "mPaaS_SDWebImage"

```
4. 去除 mPaaS 的组件库后，可自行使用 pod install 命令引入原生的版本。

### 库课网校冲突汇总

1. 非 mPaaS 定制库冲突

```objc
remove_pod "mPaaS_SDWebImage"
remove_pod "mPaaS_Masonry"
remove_pod "mPaaS_MBProgressHud"
```
2. mPaaS 定制库冲突

```objc
mPaaS_pod "mPaaS_AlipaySDK" // 阿里支付SDK 

```
> 删除KKThridParty组件中支付宝SDK

> 删除PolyvAliHttpDNS库中的UTDID库 

> 修改kkios_CC_Video组件库中 DWNetworkMonitor中类SimplePing名称 SimplePing -> KKSimplePing

解决冲突之后podfile文件修改如下：

```objc
# mPass
    remove_pod "mPaaS_SDWebImage"
    remove_pod "mPaaS_Masonry"
    remove_pod "mPaaS_MBProgressHud"
    # 小程序：小程序集成发布能力。
    mPaaS_pod "mPaaS_TinyApp"
    # H5 容器与离线包
    mPaaS_pod "mPaaS_Nebula"
    # 支付宝SDK
    mPaaS_pod "mPaaS_AlipaySDK"
```



