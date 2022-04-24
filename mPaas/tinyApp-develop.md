# 小程序接入

## 接入 iOS

接入方式：使用 cocoapods-mPaaS 插件。
1. 在 Podfile 文件中，使用 mPaaS_pod "mPaaS_TinyApp" 添加小程序组件依赖

```swift
mPaaS_pod "mPaaS_TinyApp"
```

2. 在命令行中执行 pod install 即可完成接入。

### 使用小程序SDK
小程序的整个使用过程主要分为以下三步：
1. 初始化配置
2. 发布小程序
3. 启动小程序

#### 1. 初始化配置
##### 1.1 启动容器

- 为了使用 Nebula 容器，您需要在程序启动完成后调用 SDK 接口，对容器进行初始化。必须在 DTFrameworkInterface 的 - (void)application:(UIApplication *)application beforeDidFinishLaunchingWithOptions:(NSDictionary *)launchOptions 中进行初始化

```swift
- (void)application:(UIApplication *)application beforeDidFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    // 初始化容器
    [MPNebulaAdapterInterface initNebula];
}
```
- 若您需要使用 预置小程序包、自定义 JSAPI 和 Plugin 等功能，请将上方代码中的 initNebula 替换为下方代码中的 initNebulaWith 接口，传入对应参数对容器进行初始化。

> presetApplistPath：自定义的预置小程序包的包信息路径。<br>
> appPackagePath：自定义的预置小程序包的包路径。<br>
> pluginsJsapisPath：自定义 JSAPI 和 Plugin 文件的存储路径


```swift
  - (void)application:(UIApplication *)application beforeDidFinishLaunchingWithOptions:(NSDictionary *)launchOptions
  {
      // 初始化容器
      NSString *presetApplistPath = [[NSBundle mainBundle] pathForResource:[NSString stringWithFormat:@"MPCustomPresetApps.bundle/h5_json.json"] ofType:nil];
      NSString *appPackagePath = [[NSBundle mainBundle] pathForResource:[NSString stringWithFormat:@"MPCustomPresetApps.bundle"] ofType:nil];
      NSString *pluginsJsapisPath = [[NSBundle mainBundle] pathForResource:[NSString stringWithFormat:@"Poseidon-UserDefine-Extra-Config.plist"] ofType:nil];
      [MPNebulaAdapterInterface initNebulaWithCustomPresetApplistPath:presetApplistPath customPresetAppPackagePath:appPackagePath customPluginsJsapisPath:pluginsJsapisPath];
  }
```
- 配置小程序包请求时间间隔：mPaaS 支持配置小程序包的请求时间间隔，可全局配置或单个配置。
全局配置：您可以在初始化容器时通过如下代码设置⼩程序包的更新频率

```swift
[MPNebulaAdapterInterface shareInstance].nebulaUpdateReqRate = 7200;
```
> 其中 7200 是设置全局更新间隔的值，7200 为默认值，代表间隔时长，单位为秒，您可修改此值来设置您的全局小程序包请求间隔，范围为 0 ~ 86400 秒（即 0 ~ 24 小时，0 代表无请求间隔限制）

##### 1.2 定制容器
如有需要，您可以通过设置 MPNebulaAdapterInterface 的属性值来定制容器配置。必须在 DTFrameworkInterface 的 - (void)application:(UIApplication *)application afterDidFinishLaunchingWithOptions:(NSDictionary *)launchOptions 中设置，否则会被容器默认配置覆盖。

```swift
  - (void)application:(UIApplication *)application afterDidFinishLaunchingWithOptions:(NSDictionary *)launchOptions
  {
      // 定制容器
      [MPNebulaAdapterInterface shareInstance].nebulaVeiwControllerClass = [H5WebViewController class];
      [MPNebulaAdapterInterface shareInstance].nebulaNeedVerify = NO;
      [MPNebulaAdapterInterface shareInstance].nebulaUserAgent = @"mPaaS/Portal";
  }
```
属性含义如下：
| 名称  | 含义  | 备注  |
|---|---|---|
| nebulaVeiwControllerClass  | H5 页面的基类  | 默认为 H5WebViewController。若需指定所有 H5 页面的基类，可直接设置此接口。注意：基类必须继承自 H5WebViewController。  |
| nebulaWebViewClass  | 设置 WebView 的基类  | 基线版本大于 10.1.60 时，默认为 H5WKWebView。自定义的 WebView 必须继承 H5WKWebView。基线版本等于 10.1.60 时，不支持自定义。  |
|  nebulaUseWKArbitrary |  设置是否使用 WKWebView 加载小程序包页面 | 基线版本大于 10.1.60 时，默认为 YES。基线版本等于 10.1.60 时，默认为 NO。  |
| nebulaNeedVerify  | 是否验签，默认为 YES  |  若 配置小程序包 时未上传私钥文件，此值需设为 NO，否则小程序包加载失败。 |
| nebulaPublicKeyPath  |  小程序包验签的公钥 |  与 配置小程序包 时上传的私钥对应的公钥。 |
|  errorHtmlPath |  当 H5 页面加载失败时展示的 HTML 错误页路径 | 默认读取 MPNebulaAdapter.bundle/error.html  |
| configDelegate  | 设置自定义开关 delegate  | 提供全局修改容器默认开关值的能力。  |

##### 1.3 更新小程序包
启动完成后，全量或者单个应用请求小程序包信息，检查服务端是否有更新包。为了不影响应用启动速度，建议在 (void)application:(UIApplication \*)application afterDidFinishLaunchingWithOptions:(NSDictionary \*)launchOptions 之后调用。

```Object-C
- (void)application:(UIApplication *)application afterDidFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
   // 单个应用请求
[[MPNebulaAdapterInterface shareInstance] requestNebulaAppsWithParams:@{@"2018080616290001":@"*"} finish:^(NSDictionary *data, NSError *error) {
    }];
    // 全量更新小程序包
    [[MPNebulaAdapterInterface shareInstance] requestAllNebulaApps:^(NSDictionary *data, NSError *error) {
        NSLog(@"");
    }];
}
```

> {appid:version},可传多个appid,不指定version时传空传,默认取最高版本 <br>
 支持版本号模糊匹配 e.g. '*' 匹配最高版本号 '1.*' 匹配1开头的版本号总最高版本号等,最长4位

##### 1.4 非框架托管配置

非框架托管应用初始化 mPaaS 框架的简易方法如下：
- 只需在应用的 window 及 navigationController 创建完成后，调用以下方法即可，不再需要创建 bootloader、隐藏框架 window 等操作。


```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // Override point for customization after application launch.
        self.window = UIWindow.init(frame: UIScreen.main.bounds)
        self.window?.rootViewController = UINavigationController.init(rootViewController: ViewController.init())
        self.window?.makeKeyAndVisible()
        // navigationcontroller创建完成后，启动 mPaaS 框架
        DTFrameworkInterface.sharedInstance().manualInitMpaasFramework(with: application, launchOptions: launchOptions, window: self.window, navigationController: navigationController)
        return true
    }

```
- 支持不继承 DFNavigationController。

```
在 DTFrameworkInterface (Category)中设置
- (BOOL)shouldInheritDFNavigationController {
    return NO;
}
```
