# PocketSDK-iOS接入文档
___

## 一、导入PocketSDK

* 将PocketSDK文件夹拖入工程。

* 将配置文件 PocketSDKConfig_(appId).plist 拖入工程。

* 点击 *TARGETS—> Build Phases —> Link Binary With Libraries* 添加以下库:

	* AdSupport.framework
	* SystemConfiguration.framework 
	* MobileCoreServices.framework 
	* CoreTelephony.framework 
	* libz.dylib 
	* StoreKit.framework 
	* Security.framework
<p></p>
* 点击 *TARGETS—> Build Settings*，搜索 *Other Linker Flags*，添加`-ObjC`。

* 点击 *PROJECT —> Build Settings*，搜索 *Other C Flags*，添加`-DSQLITE_HAS_CODEC`。

* 点击 *TARGETS—> Info*，在 *URL Types* 中添加项，参数URL Scheme填写`fb311294709063394`。

* `#import “PocketSDKManager.h` 。

> **注意事项**
> 
> **①. SDK使用了SQLCipher加密数据库,工程中无需也不可添加libsqlite3.0.dylib或 libsqlite3.dylib。<br/>
> ②. 如果项目工程中使用了libcurl.a (使用cocos2dx开发的游戏,大部分会用到该静态库),请将静态库 libcrypto.a从目录PoketSDK/SQLCipher/下删除。**

## 二、PocketSDK开放接口

* #### SDK初始化、生命周期方法、代理方法实现

	AppDelegate.h

		#import <UIKit/UIKit.h>
		#import "PocketSDKManager.h"
		
		@interface AppDelegate : UIResponder <UIApplicationDelegate, PocketSDKDelegate>
		
		@property (strong, nonatomic) UIWindow *window;
		
		@end
		
	AppDelegate.m

		#import "AppDelegate.h"
		
		@implementation AppDelegate
		
		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions: (NSDictionary *)launchOptions {
			
			self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
			self.window.backgroundColor = [UIColor whiteColor];
			[self.window makeKeyAndVisible];
			...
			self.window.rootViewController = rootVC;
		
		/* ------------------------------------------------- *\
		   ------------------ SDK初始化方法 ------------------
		\* ------------------------------------------------- */
		
			/* 发布至AppStore的包（官方包）可不调用。
			 * 一般官方包的channelId设置为0（默认值），企业包为-1。
			 * 【注意】如若调用，请在SDK初始化方法之前调用！ 
			 */
			[PocketSDKManager setChannelId:0];
			
			/* 必须调用。
			 * SDK初始化方法。 
			 */
			[PocketSDKManager shareManagerWithServerAddress:@"<SDK服务器地址>"
		                                              appId:@"<分配的AppId>"
		                                             appKey:@"<分配的AppKey>"];
		
			/* 可不调用。
			 * 设置SDK显示模式为横屏或竖屏，未设置则SDK将自动进行匹配。 
			 */                                     
			[PocketSDKManager setDisplayMode:PocketSDKDisplayModeLandscape];
			
			/* 必须调用。
			 * 设置代理，代理对象需实现协议<PocketSDKDelegate>中的方法。 
			 */
			[PocketSDKManager setDelegate:self];
			
			...
		}
		
		/* ------------------------------------------------- *\
		   ------------------ SDK生命周期方法 ----------------
		\* ------------------------------------------------- */
		
		- (void)applicationDidEnterBackground:(UIApplication *)application {
		
			/* 必须调用。 
			 */
			[PocketSDKManager applicationDidEnterBackground];
		}
		
		- (void)applicationWillEnterForeground:(UIApplication *)application {
		
			/* 必须调用。 
			 */
			[PocketSDKManager applicationWillEnterForeground];
		}
		
		- (void)applicationDidBecomeActive:(UIApplication *)application {
		
		    /* 必须调用。 
		     */
			[PocketSDKManager applicationDidBecomeActive];
		}
		
		- (BOOL)application:(UIApplication *)application 
					openURL:(NSURL *)url
		  sourceApplication:(NSString *)sourceApplication 
				 annotation:(id)annotation {
				 
		    /* 必须调用。 
		     */
		    return [PocketSDKManager handleOpenURL:url sourceApplication:sourceApplication];
		}
		
		/* ------------------------------------------------- *\
		   ------------------- SDK代理方法 -------------------
		\* ------------------------------------------------- */
		
		#pragma mark - PocketSDKDelegate
		
		/* 必须实现的代理方法。
		 * 应该能够随时返回一个每次都不相同的游戏交易id。
		 * 可以选择下列两种方式实现：
		 * ①. 从游戏服务器同步获取并返回。
		 * ②. 返回一个之前已经获取并被持有待用的交易id（返回后不再持有），随后从游戏服务器异步获取新的交易id持有待用。
		 * 不能返回有效gameOrderId则返回nil。 
		 */
		- (NSString *)gameOrderId {
		 
			return @"游戏交易id";
		}
		
		/* 必须实现的代理方法。
		 * 应该能够返回游戏内的角色信息。
		 * PocketSDKRoleId: 角色id; PocketSDKRoleLevel: 角色等级; PocketSDKRoleZoneId: 角色所在区服。 
		 */
		- (NSDictionary *)roleInfoInTheGame {
		    
		    return @{PocketSDKRoleId:@"9527",PocketSDKRoleLevel:@"60",PocketSDKRoleZoneId:@"1001"};
		}
		
		/* 必须实现的代理方法。
		 * 切换账号。此处应实现注销游戏角色、返回登录界面,同时弹出SDK登录窗口（showLoginView）等操作。 
		 */
		-(void)switchUser {
		    
		    ...
			[PocketSDKManager showLoginView];
		}
		
		/* 登录成功的代理方法。
		 * 会返回平台userId和token。 
		 */
		- (void)loginDidSuccessWithUserInfo:(NSDictionary *)userInfo {
		    
		    NSString *userId = userInfo[@"userId"];
		    NSString *token = userInfo[@"token"];
		    ...
		}
		
		/* 可不实现的代理方法。
		 * 可以在该方法中处理SDK初始化失败的后续操作，调用Block: retryInit();可以重新初始化SDK。
		 * 若不实现该方法，则当SDK初始化失败时，SDK会自行弹出重试界面及处理后续逻辑。
		 */
		- (void)initSdkDidFailWithRetry:(void (^)())retryInit {
			
			#error 若不实现该方法，代理对象中不应有此方法（空方法也是一种实现）。
		}
		
		/* 可选实现的代理方法。
		 * SDK支付界面已经关闭的代理方法。
		 * 可以在该方法中刷新游戏界面，以便在游戏内体现充值结果（道具或虚拟货币增加）。 
		 */
		- (void)paymentViewDidClose {
	
			...
		}
	
		@end

* #### 绑定区服接口

		- (void)someFunction {
			...
			/* 必须调用。
			 * 在游戏角色进入游戏后调用此方法。
			 * 必须保证在调用该方法时，代理方法- (NSDictionary *)roleInfoInTheGame;已经能够返回角色信息。 
			 */
			[PocketSDKManager roleDidEnterGame];
			...
		}

* #### 界面相关
		
		- (void)someFunction {
			...
			/* 游戏启动后,调用该方法进行登录。
			 * 若用户之前登录过则会自动登录，否则会弹出登录界面。 
			 */
			[PocketSDKManager tryAutoLoginElseShowLoginView];
			...
			/* 调用该方法显示SDK支付界面。 
			 */
			[PocketSDKManager showPaymentView];
			...
			/* 游戏可在需要时隐藏悬浮按钮，但应及时恢复悬浮按钮的显示。 
			 */
			[PocketSDKManager hideFloatButton];
			[PocketSDKManager showFloatButton];
			...
		}
	
## 三、本地化（国际化）

> **PocketSDK支持多种语言，目前支持英文、简体中文、繁体中文、越南语、泰语。对于SDK支持的语言，根据设备设置的语言不同，SDK界面文字会采用相应的语言显示，但前提是游戏工程已经添加了该语言的本地化文件。**

为了使SDK支持某种语言显示，可以按照以下步骤为游戏工程添加本地化文件。

1. 在工程中新建一个Strings File文件，可以命名为Localizable。

2. 选中Localizable.strings,打开Xcode的右侧边栏,点击 *Localization* 中的 *Localize...* 按键。

	![](http://i2.tietuku.com/38bb53cc0ec0e1e0.png)

3. 在 *PROJECT -> Info* 的 *Localizations* 中添加希望支持的语言。

## 四、真机调试、生成ipa文件

* #### p12和mobileprovision文件

	在首次提供SDK时，会提供2个p12文件和3个mobileprovision文件。均双击（使用xcode）安装即可。
	
	真机调试或打包前需要配置 *TARGETS -> Build Settings* 中的参数 *Provisioning Profile* 和 *Code Signing Identity*。
	
	*Provisioning Profile* 的debug参数选择 (projectName)\_Dev<br/>
	 
	*Provisioning Profile* 的release参数选择 (projectName)\_AdHoc<br/>**出提审包时需改为 (projectName)\_AppStore**

	分别点击 *Code Signing Identify* 的 *debug* 和 *release* 选择（提示的）与之匹配的 *Code Signing Identity*。
	
	![](http://i2.tietuku.com/520d033d21f089d3.png)
	
	另外，设置 *TARGETS -> General* 中的 *Bundle identifier* 为平台提供的 BundleId。

* #### 生成ipa文件

	从Xcode6开始通过 *Product -> Archive* 打包工程需要使用开发者账号,所以请使用xcodebuild 进行打包。