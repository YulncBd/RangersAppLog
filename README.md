# RangersAppLog

## Demo演示

1. `git clone git@github.com:bytedance/RangersAppLog.git`
2. `cd RangersAppLog/Eample`
3. `pod install`
4. `open RangersAppLog.xcworkspace`


## 要求

特别说明，只支持Xcode 11 打包开发，Xcode 11以下，请单独联系开发提供SDK包

* iOS 8.0+
* Xcode 11

## 版本说明

1. Lite版，埋点版本，即 `subspecs => ['Core']`
2. 无埋点版本，即 `subspecs => ['UITracker']`
3. 圈选版本，即 `subspecs => ['Picker']`

## 集成方式


或者参照Demo工程的Podfile

```Rbuy
source 'https://github.com/bytedance/cocoapods_sdk_source_repo.git'

# 接入无埋点版本
target 'YourTarget' do
  pod 'RangersAppLog', '~> 5.1'
end

# 接入埋点版本 
target 'YourTarget' do
  pod 'RangersAppLog', '~> 5.1',:subspecs => ['Core']
end

```

## 集成指南

更多接口参见头文件，和Demo工程.

### 初始化SDK

```Objective-C

#import <RangersAppLog/RangersAppLog.h>

- (void)startAppLog {
    BDAutoTrackConfig *config = [BDAutoTrackConfig new];
    config.appID = @"159486";
    config.channel = @"App Store";
    config.appName = @"dp_tob_sdk_test2";
	 config.autoTrackEnabled = YES;
    /// show debug log
    config.showDebugLog = YES;
    config.logger = ^(NSString * _Nullable log) {
        NSLog(@"%@",log);
    };
    /// change to your UserUniqueID if now is loged in
    NSString *uniqueID = @"12345";
    BDAutoTrack *track = [BDAutoTrack trackWithConfig:config];
    /// change to your UserUniqueID if now is loged in
    NSString *uniqueID = @"12345";
    [track setCurrentUserUniqueID:uniqueID];
    [track startTrack];
    
    self.track = track;
}

```

### 用户态变化

```Objective-C

- (void)logout {
    [self.track clearUserUniqueID];
}

- (void)login {
    /// change to your UserUniqueID
    NSString *uniqueID = @"12345";
    [self.track setCurrentUserUniqueID:uniqueID];
}

```

### 埋点事件上报

```Objective-C

+ (void)eventV3:(NSString *)event params:(NSDictionary *)params {
    [self.track eventV3:event params:params];
}

```

### Scheme上报

```Objective-C

#import <RangersAppLog/RangersAppLog.h>

/// 如果是iOS 13中重写UISceneDelegate的回调，则按照i以下code
- (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts {
    for (UIOpenURLContext *context in URLContexts) {
        NSURL *URL = context.URL;
        if ([[BDAutoTrackSchemeHandler sharedHandler] handleURL:URL appID:@"appid" scene:scene]) {
            continue;
        }

        /// your handle code for the URL
    }
}

/// 如果是iOS 13一下，重写UIApplicationDelegate的回调方法，则参考以下code
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey, id> *)options {
    if ([[BDAutoTrackSchemeHandler sharedHandler] handleURL:url appID:@"appid" scene:nil]) {
        return YES;
    }

    /// your handle code

    return NO;
}

```

## 版本更新记录

### 5.1.1
- 新增AB功能开关

### 5.0.0
- 修复ABTestConfigValueForKey接口defaultValue问题

### 4.5.0

- 解决无埋点hook与ReSwift、RxCocoa、RxDataSources等第三方SDK的冲突
- 移除ABVersion相关的接口
- 优化OOM问题
- 修复IGListKit冲突


## 证书

本项目使用[MIT 证书](LICENSE)。详细内容参见[证书](LICENSE)文件。
