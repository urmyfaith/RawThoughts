<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table Of Contents**

- [读 AFNetworking 源码之 Reachability](#%E8%AF%BB-afnetworking-%E6%BA%90%E7%A0%81%E4%B9%8B-reachability)
  - [Reachability 是什么？](#reachability-%E6%98%AF%E4%BB%80%E4%B9%88)
  - [Reachability 的几个实现](#reachability-%E7%9A%84%E5%87%A0%E4%B8%AA%E5%AE%9E%E7%8E%B0)
  - [AFNetworkReachabilityManager](#afnetworkreachabilitymanager)
    - [使用及注意事项](#%E4%BD%BF%E7%94%A8%E5%8F%8A%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9)
      - [1. 初始化对象](#1-%E5%88%9D%E5%A7%8B%E5%8C%96%E5%AF%B9%E8%B1%A1)
      - [2. 开启监听](#2-%E5%BC%80%E5%90%AF%E7%9B%91%E5%90%AC)
      - [3. 接收通知](#3-%E6%8E%A5%E6%94%B6%E9%80%9A%E7%9F%A5)
      - [4. 停止监听](#4-%E5%81%9C%E6%AD%A2%E7%9B%91%E5%90%AC)
    - [实现](#%E5%AE%9E%E7%8E%B0)
    - [总结](#%E6%80%BB%E7%BB%93)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# 读 AFNetworking 源码之 Reachability

## Reachability 是什么？

根据 Mac 自带的词典，这个单词的英英解释是 `able to be contacted`，通俗一点就是连接的能力。而放到 App 中就是有没有连接网络的能力。

相信大家在日常使用应用的时候都看到过。在不同的网络条件下，App 的某些行为会有所不同。比如对于多媒体素材来说，移动网络下通常会请求低质量版本，而 Wi-Fi 下会尽量下载大尺寸甚至原尺寸。还有，在 Wi-Fi 条件下播放视频的时候，如果用户的 Wi-Fi 突然断掉了，那 App 应该自动暂停视频播放及预取缓存，并弹窗提示用户。

总结一下，当你想知道当前的设备是连着 Wi-Fi 还是连着 4G，或者你想在用户设备网络条件发生变化的时候得到通知，那你就该使用 Reachability 了。

## Reachability 的几个实现

目前网络上大概有三种实现

- [Apple Sample Code - Reachability](https://developer.apple.com/library/content/samplecode/Reachability/Introduction/Intro.html)
- [AFNetworking - AFNetworkReachabilityManager](https://github.com/AFNetworking/AFNetworking/blob/master/AFNetworking/AFNetworkReachabilityManager.h)
- [tonymillion/Reachability](https://github.com/tonymillion/Reachability)

而它们本质上都是对 SystemConfiguration Framework 中 `SCNetworkReachability.h` 相关的 C API 进行封装，以实现相同的效果。

## AFNetworkReachabilityManager

由于三个封装的原理以及实现的功能都大同小异，但在这里会详细介绍一下 AFNetworking（用 Objective-C 开发的同学，项目里应该 95% 以上都会依赖 AFNetworking 吧）版本的使用以及实现。

### 使用及注意事项
#### 1. 初始化对象
AFNetworkReachabilityManager 比较常用的几个初始化方法如下，

```objc
/// 获取全局唯一单例
+ (instancetype)sharedManager;
/// 实例化一个监听默认地址（0.0.0.0）的 manager
+ (instancetype)manager;
/// 实例化一个监听给定 domain 的 manager
+ (instancetype)managerForDomain:(NSString *)domain;
```

>Note: Reachability cannot tell your application if you can connect to a particular host, only that an interface is available that might allow a connection, and whether that interface is the WWAN.

首先要澄清一个概念，根据 Apple 的文档，Reachability 并不能像 `ping` 一样，所以在天朝不要看到 Reachability 能够通过 Wi-Fi 连接到 `www.youtube.com` 就妄想你可以上 YouTube 了，你只是**可能**通过 Wi-Fi 连接到那个 host，而不是**一定**可以连接上。

这里比较建议调用 `+ (instancetype)manager` 创建属于自己的 manager，好处我们后面讲。

#### 2. 开启监听

首先需要调用开启监听的方法，

```objc
/// 这是很容易被忽视的方法，不是初始化了 manager 就默认开始监听的
- (void)startMonitoring;
```

如果你只想知道当前的网络条件是什么，通过 manager 的下面三个属性就可以了解了，

```
BOOL reachable;         // 有无网络连接
BOOL reachableViaWWAN   // 当前是移动网络
BOOL reachableViaWiFi   // 当前是 Wi-Fi
```

#### 3. 接收通知

AFNetworking 的 Reachability Manager 提供了两种获取网络条件发生变化的方式，

```objc
/// 通过设置 manager 的 statusChangeBlock，监听变化
- (void)setReachabilityStatusChangeBlock:(nullable void (^)(AFNetworkReachabilityStatus status))block;

/// 通过监听一个 manager 发出的 notification，userinfo 中的
/// `AFNetworkingReachabilityNotificationStatusItem` key 就是当前的 status
FOUNDATION_EXPORT NSString * const AFNetworkingReachabilityDidChangeNotification;
```

这里就要解释一下上文中提到问题，为什么不建议使用 `+ (instancetype)sharedManager;` 获得单例

因为 `setReachabilityStatusChangeBlock` 的回调通知的方式只会对最新调用的地方才会生效，如果大家都用 `sharedManager` 获取 manager，然后在自己的业务里设置回调的 block，如果此时多个业务都跑着，那在网络条件发生变化的时候，那也只有在最后被执行到的代码才会获得通知，之前所有设置 block 的地方都会失效，为了避免这种小概率的事情发生，还是应该在自己的业务里创建自己的 manager。

#### 4. 停止监听

不需要监听网络条件发生变化的时候调用此方法，

```objc
- (void)stopMonitoring;
```

### 实现

先来分析一下 manager 的初始化。它有一个 designated initializer，也就是所有的初始化方法都会最终调用这个方法进行初始化（顺便帮我们复习了一把初始化方法的设计），

```objc
- (instancetype)initWithReachability:(SCNetworkReachabilityRef)reachability {
    self = [super init];
    if (!self) {
        return nil;
    }

    _networkReachability = CFRetain(reachability);
    self.networkReachabilityStatus = AFNetworkReachabilityStatusUnknown;

    return self;
}
```

它接受一个被定义在 `SystemConfiguration/SystemConfiguration.h` 中 `SCNetworkReachabilityRef` 的结构体指针为参数。

看到以 Ref 结尾的对象，就要想到它是 Core Foundation 中的对象类型，这些对象基本上是 C 的指针，编译器不会帮你进行内存管理，所以需要调用 CF 开头的相关 API 自己手动进行内存管理。

由于 manager 持有一个 `SCNetworkReachabilityRef` 的对象，所以在这个初始化方法里，它对这个参数进行了 retain 操作，同样地，在 `dealloc` 中调用了 release 方法对资源进行释放。

```objc
- (void)dealloc {
    [self stopMonitoring];
    
    // 注意， C 对象的判空要和 NULL 比较
    if (_networkReachability != NULL) {
        CFRelease(_networkReachability);
    }
}
```

其它的几个 convenience initializers 就是针对不同的需求创建不同的 `SCNetworkReachabilityRef` 指针。

```objc
- (void)startMonitoring {
    /// 在启用某个动作之前先关闭这个动作是一种非常好的编程习惯
    /// 防止多次调用某个方法会发生副作用
    [self stopMonitoring];

    if (!self.networkReachability) {
        return;
    }

    __weak __typeof(self)weakSelf = self;
    AFNetworkReachabilityStatusBlock callback = ^(AFNetworkReachabilityStatus status) {
        __strong __typeof(weakSelf)strongSelf = weakSelf;
        /// 通知 block 的持有者，当前的网络状态
        strongSelf.networkReachabilityStatus = status;
        if (strongSelf.networkReachabilityStatusBlock) {
            strongSelf.networkReachabilityStatusBlock(status);
        }

    };

    /// 创建需要的 context，这个结构体包含了用户指定的信息以及 callback
    SCNetworkReachabilityContext context = {0, (__bridge void *)callback, AFNetworkReachabilityRetainCallback, AFNetworkReachabilityReleaseCallback, NULL};
    /// 当 self.networkReachability 的状态发生变化时，将会调用 AFNetworkReachabilityCallback 函数指针
    /// AFNetworkReachabilityCallback 的第三个参数将会被传入上面创建的 context 结构体中的 callback 信息
    SCNetworkReachabilitySetCallback(self.networkReachability, AFNetworkReachabilityCallback, &context);
    /// 开始监听 self.networkReachability，需要指定 runloop 和 runloop mode
    /// Apple 使用的是 kCFRunLoopDefaultMode
    SCNetworkReachabilityScheduleWithRunLoop(self.networkReachability, CFRunLoopGetMain(), kCFRunLoopCommonModes);

    /// 使用后台线程获取当前持有的 SCNetworkReachabilityRef 的网络条件
    /// 更新当前 manager 的 status
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_BACKGROUND, 0),^{
        SCNetworkReachabilityFlags flags;
        if (SCNetworkReachabilityGetFlags(self.networkReachability, &flags)) {
            AFPostReachabilityStatusChange(flags, callback);
        }
    });
}
```

```objc
/// 在 `SCNetworkReachabilitySetCallback(self.networkReachability, AFNetworkReachabilityCallback, &context);` 的时候设置上了系统回调的函数指针
/// 网络条件发生变化时，此函数被调用，同时获取到了那个持有 SCNetworkReachabilityRef 的 manager 的 block
static void AFNetworkReachabilityCallback(SCNetworkReachabilityRef __unused target, SCNetworkReachabilityFlags flags, void *info) {
    AFPostReachabilityStatusChange(flags, (__bridge AFNetworkReachabilityStatusBlock)info);
}

static void AFPostReachabilityStatusChange(SCNetworkReachabilityFlags flags, AFNetworkReachabilityStatusBlock block) {
    AFNetworkReachabilityStatus status = AFNetworkReachabilityStatusForFlags(flags);
    dispatch_async(dispatch_get_main_queue(), ^{
        if (block) {
            /// 调用那个 manager 的 block，实现通知
            block(status);
        }
        /// 通知 notification 监听者
        NSNotificationCenter *notificationCenter = [NSNotificationCenter defaultCenter];
        NSDictionary *userInfo = @{ AFNetworkingReachabilityNotificationStatusItem: @(status) };
        [notificationCenter postNotificationName:AFNetworkingReachabilityDidChangeNotification object:nil userInfo:userInfo];
    });
}
```

### 总结

AFNetworking 的 Reachability 封装的比 Apple 好的地方在于，它不光实现了通过 notification 的方式通知网络变化，还实现了 block 的方式，代码干净，文档丰富。一个 iOS 届最知名的开源库绝非浪得虚名。































