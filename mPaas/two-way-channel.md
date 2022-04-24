# iOS 小程序自定义双向通道

## 小程序调用原生自定义 API

1. 客户端自定义 API 并注册。

参考 [$\color{red}{自定义 JSAPI}$](https://gitee.com/ylyk/technology-share/blob/master/mPaas/custom-jsapi.md)，注册您的自定义 API。

2. 小程序调用。


```js
 // 小程序向原生进行通信
  tinyToNative() {
    my.call('tinyToNative', {
      name: 'xufeng',
      age: '18'
    }, (result) => {
      console.log(result);
      my.showToast({
        type: 'none',
        content: JSON.stringify(result),
        duration: 3000,
      });
    })
  },
```

## 原生应用向小程序发送自定义事件

1. 小程序注册事件。

```js
// 小程序注册事件
    my.on('nativeToTiny', (res) => {
      my.showToast({
        type: 'none',
        content: JSON.stringify(res),
        duration: 3000,
        success: () => {
        },
        fail: () => {
        },
        complete: () => {
        }
      });
    })
```
2. 客户端发送事件

获取当前小程序页面所在的 viewController，调用 callHandler 方法发送事件。

```objc
@implementation KKJsApi4GetAppInfo

- (void)handler:(NSDictionary *)data context:(PSDContext *)context callback:(PSDJsApiResponseCallbackBlock)callback {
    [super handler:data context:context callback:callback];
    NSDictionary *infoDictionary = [[NSBundle mainBundle] infoDictionary];
    NSString *app_Name = [infoDictionary objectForKey:@"CFBundleDisplayName"];
    NSString *app_Version = [infoDictionary objectForKey:@"CFBundleShortVersionString"];
    NSString *app_build = [infoDictionary objectForKey:@"CFBundleVersion"];
    NSString* deviceName = [[UIDevice currentDevice] systemName];
    
    NSMutableDictionary *appInfoDict = [NSMutableDictionary dictionary];
    [appInfoDict setObject:@"appName" forKey:app_Name];
    [appInfoDict setObject:@"appVersion" forKey:app_Version];
    [appInfoDict setObject:@"appBuild" forKey:app_build];
    [appInfoDict setObject:@"deviceName" forKey:deviceName];
   
    KKH5WebViewController *h5VC;
    for (id event in context.eventTargetList) { // 事件链路
        if ([event isKindOfClass:[PSDScene class]]) {
            h5VC = (KKH5WebViewController *)[event viewController];
        }
    }
    [h5VC callHandler:@"nativeToTiny" data:appInfoDict responseCallback:^(id responseData) {
            
    }];
}
@end 
```



