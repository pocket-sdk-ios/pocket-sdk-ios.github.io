# SDK-iOS 3.1升级指南  

> SDK3.1更新了数据统计的SDK，并且新增或调整了接口，请参照本文档升级SDK。

* 删除工程中的原PocketSDK相关文件。

* 导入新的PocketSDK文件夹（及其内的文件）。

* 导入新的配置文件PocketSDKConfig_(appId).plist，建议将原文件彻底删除，以免将来误用。

* PocketSDK3.1较之前的SDK版本API有所变动，需要游戏适当修改。

	①. 取消方法 `+ (instancetype)shareManager;`。

	②. 取消以下代理属性：

  		@property (nonatomic, weak, readwrite) id <PocketSDKRequiredDelegate> requiredDelegate;
 		 @property (nonatomic, weak, readwrite) id <PocketSDKLoginDelegate> loginDelegate;
  		@property (nonatomic, weak, readwrite) id <PocketSDKSwitchUserDelegate> switchUserDelegate;
  		@property (nonatomic, weak, readwrite) id <PocketSDKPaymentDelegate> paymentDelegate;
而使用方法`+ (void)setDelegate:(id<PocketSDKDelegate>)delegate;`设置代理。
响应地，协议也合并为 *`<PocketSDKDelegate>`* 一个。

	③. 新增可选实现的代理方法 `- (void)initSdkDidFailWithRetry:(void (^)())retryInit;`，可自定义SDK初始化失败的处理逻辑。

	④. 新增横竖屏模式设置方法 `+ (void)setDisplayMode:(PocketSDKDisplayModes)displayMode;`。

详细内容可参见文档[《pocketsdk-ios接入指南》](/Documents/docking.html)。