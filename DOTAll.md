[![Version](https://img.shields.io/cocoapods/v/DOT.svg?style=flat)](https://cocoapods.org/pods/DOT)
[![License](https://img.shields.io/cocoapods/l/DOT.svg?style=flat)](https://cocoapods.org/pods/DOT)
[![Platform](https://img.shields.io/cocoapods/p/DOT.svg?style=flat)](https://cocoapods.org/pods/DOT)

# Index
* [DOT](./README.md#DOT)
	* [SDK 다운로드 및 설치](./README.md#DOT_INSTALL)
		* [SDK 다운로드](./README.md#DOT_SDK_DOWNLOAD)
		* [SDK 세팅](./README.md#DOT_infoplist)
	* [기본 분석 적용](./README.md#DOT_BASE)
		* [SDK init](./README.md#DOT_INIT)
		* [방문 및 페이지 분석](./README.md#--방문-및-페이지-분석)
	* [Hybrid 앱 분석 방법](./README.md#DOT_HYBRID)
	* [유입 경로 분석](./README.md#DOT_ROUTE)
		* [앱 설치 경로 분석](./README.md#--앱-설치-경로-분석)
		* [외부 유입 경로 분석 (Deeplink)](./README.md#DOT_DEEPLINK))
		* [Facebook 분석](./README.md#--Facebook-분석)
		* [푸시 메시지 분석](./README.md#--푸시-메시지-분석)
	* [고급 컨텐츠 분석](./README.md#DOT_ADVANCE)
		* [로그인 분석](./README.md#DOT_USER)
		* [in-App 분석](./README.md#in-App-분석)
		* [Micro Conversion 분석](./README.md#Micro-Conversion-분석)
		* [Purchase 분석](./README.md#Purchase-분석)   
		
## DOT

### <a id="DOT_INSTALL"></a> SDK 다운로드 및 설치

##### <a id="DOT_SDK_DOWNLOAD"></a> - SDK 다운로드
- XCode CocoaPad 환경에서 SDK 다운로드 방법
XCode 프로젝트 파일중 Podfile 파일에 다음과 같이 SDK를 추가합니다.

```
pod 'DOT' ~> 'x.x.x'
```

Podfile 에 해당라인을 추가한 후 Terminal 프로그램을 실행하여 다음의 명령을 수행합니다.
```
cmd> pod install
```

만약 Cocoapad 환경의 프로젝트가 아닌 경우에는, 아래의 Github 링크에서 SDK Library 파일을 다운로드 가능합니다.
다운로드한 Framework 파일을 XCode 프로젝트에서 참조 가능하도록 설정 하시기 바랍니다.


[DOT SDK 다운로드 받기](https://github.com/WisetrackerTechteam/release_sdk_v2_ios.git)

##### <a id="DOT_infoplist"></a> - SDK 세팅
XCode 프로젝트의 info.plist 파일에 제공받은 App Analytics Key 정보를 추가합니다.
info.plist 파일을 open할때 'Property list'가 아니라 'Source Code'로 open한 후, 제공받으신 Key를 Ctrl+V 하면 됩니다.
제공받은 Key값은 아래의 예시와 같이 xml 형태를 가지고 있는 데이터 입니다.

```xml
<key>dotAuthorizationKey</key>
<dict>
    <key>domain</key>
    <string>http://dev-collector001-ncl.nfra.io/collector</string>
    <key>serviceNumber</key>
    <string>103</string>
    <key>expireDate</key>
    <string>14</string>
    <key>isDebug</key>
    <string>true</string>
    <key>isInstallRetention</key>
    <string>true</string>
    <key>isFingerPrint</key>
    <string>true</string>
    <key>accessToken</key>
    <string></string>
</dict>
```

http통신을 허용하기 위해 NSAppTransportSecurity 를 아래와 같이 추가합니다.

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

### <a id="DOT_BASE"></a> 기본 분석 적용(required)

#### <a id="DOT_INIT"></a> - SDK initialization
XCode 프로젝트의 AppDelegate 가 정의된 클래스의 **didFinishLaunchingWithOptions ** 함수에 SDK를 Initialization하기 위한 코드를 다음과 같이 적용합니다.

##### - Objective-C

```objective-c
#import <DOT/DOT.h>
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [DOT initialization];
}
```

##### - Swift

```Swift
import DOT
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
    DOT.initialization()
}
```

DOT가 사용되는 곳에서는 #import <DOT/DOT.h>, import DOT을 통해 Objective-C, Swift에 각각 import가 필요합니다. 
이하 적용 예시에서는 가독성을 위해 import하는 부분이 생략되어 있습니다.

#### - 방문 및 페이지 분석
앱의 실행 및 페이지 분석을 위해서는 각 화면의 이동시에 호출되는 Callback 함수에 다음과 같은 코드의 적용이 필요합니다
아래의 2가지 코드를 적용후에는 기본적으로 분석되는 범위는 대략적으로 다음과 같습니다

- 앱 실행 및 방문수, 일/주/월순수방문수 등 방문과 관련된 지표
- 페이지뷰, 페이지 체류시간
- 통신사, 단말기, 국가 등 방문자의 단말기 환경으로 부터 추출될 수 있는 지표
 
XCode 프로젝트의 각 View 화면별 viewWillAppear() 함수와 viewWillDisppear() 함수에 다음과 같이 기본 적인 페이지 트래픽 분석을 위한 코드를 적용합니다.

##### - Objective-C

```objective-c
- (void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];
    [DOT onStartPage];
}

- (void)viewWillDisappear:(BOOL)animated {
    [super viewWillDisappear:animated];
    [DOT onStopPage];
} 
```

##### - Swift

```Swift
override func viewWillAppear(_ animated: Bool) {
    super.viewWillAppear(animated)
    DOT.onStartPage()
}

override func viewWillDisappear(_ animated: Bool) {
    super.viewWillDisappear(animated)
    DOT.onStopPage()
}
```

### <a id="DOT_HYBRID"></a> Hybrid 앱 분석 방법
Hybrid 앱의 경우 앱 내에서 WebView 를 사용하여 웹 컨텐츠를 서비스 하기도 합니다. 이와 같이 Webview 에 의해서 보여지는 웹 컨텐츠의 경우에는 위에서 설명된 Native 화면과는 다른 방식으로 동작하기 때문에, 별도의 분석 코드 적용이 필요합니다. 분석 대상 앱이 만약 Hybrid 앱인 경우에는 아래의 코드를 참고하여 웹 컨텐츠도 분석할 수 있도록 적용을 해야합니다
 
앱내에서 사용할 WebView 의 Delegate 함수에 아래와 같이 분석코드를 적용합니다

- UIWebView를 사용할 경우 적용할 분석코드

#### - Objective-C
```objective-c
@property (nonatomic) BOOL webLoaded;

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {
    if(self.webLoaded) {  
        [DOT onStartWebPage];
    }
    self.webLoaded = NO;
    if ([[[request URL] absoluteString] hasPrefix:@"jscall-dot:"]) {
        [DOT setWebView:webView reqeust:request];
        return NO;
    }
    return YES;
} 

- (void)webViewDidFinishLoad:(UIWebView *)webView {
    if(webView.isLoading) {
        return;
    }
    self.webLoaded = YES;
}

- (void)viewDidLoad:(BOOL)animated{
    [super viewDidLoad];
    self.webLoaded = NO;
}
```

#### - Swift
```Swift
var webLoaded : Bool!
func webView(_ webView: UIWebView, shouldStartLoadWith request: URLRequest, navigationType: UIWebView.NavigationType) -> Bool {
    if webLoaded {
        DOT.onStartWebPage()
    }
    webLoaded = false
    if let unwrappedUrl = request.url {
        if unwrappedUrl.absoluteString.hasPrefix("jscall-dot:") {
            DOT.setWebView(webView, reqeust: request)
            return false
        }
    }
    return true
}

func webViewDidFinishLoad(_ webView: UIWebView) {
    if webView.isLoading {
        return
    }
    webLoaded = true
}

override func viewDidLoad() {
    super.viewDidLoad() 
    webLoaded = false 
}
```

- WKWebView를 사용할 경우 적용할 분석코드

#### - Objective-C
```Objective-C
@property (nonatomic) BOOL webLoaded;

- (void)webView:(WKWebView *)webView decidePolicyForNavigationAction:(WKNavigationAction *)navigationAction decisionHandler:(void (^)(WKNavigationActionPolicy))decisionHandler {
    if(self.webLoading) {
        [DOT onStartWebPage];
    }
    self.webLoaded = NO; 
    NSURLRequest *request = navigationAction.request;
    decisionHandler(WKNavigationActionPolicyAllow);
    if ([[[request URL] absoluteString] hasPrefix:@"jscall-dot:"]) {
        [DOT setWkWebView:webView reqeust:request];
    }
}
- (void)webView:(WKWebView *)webView didFinishNavigation:(WKNavigation *)navigation {
    if(webView.isLoading) {
        return;
    }
    self.webLoading = YES;
}
- (void)viewDidLoad:(BOOL)animated{
    [super viewDidLoad];
    self.webLoaded = NO;
}
```

#### - Swift

```Swift
var webLoaded : Bool!
func webView(_ webView: WKWebView, decidePolicyFor navigationAction: WKNavigationAction, decisionHandler: @escaping (WKNavigationActionPolicy) -> Void) {
    if webLoading {
        DOT.onStartWebPage()
    }
    webLoaded = false

    var request: URLRequest? = navigationAction.request
    decisionHandler(.allow)

    if request?.url?.absoluteString.hasPrefix("jscall-dot:") ?? false {
        DOT.setWkWebView(webView, reqeust: request)
    }
}

func webView(_ webView: WKWebView, didFinish navigation: WKNavigation!) {
    if webView.isLoading {
        return
    }
    webLoaded = true
}

override func viewDidLoad() {
    super.viewDidLoad() 
    webLoaded = false 
}
```
### <a id="DOT_ROUTE"></a> 유입 경로 분석

#### - 앱 설치 경로 분석
앱이 설치된 경로를 분석하는 시점은, 앱이 설치후 처음 실행될때 설치 경로를 획득하게 됩니다
이에 대한 처리는 기본적으로 위에서 설명된 SDK 필수 적용 사항이 적용된 상태에서는 자동으로 처리가 되기 때문에, 별도의 분석코드 적용이 필요하지 않습니다
다만, 앱의 특별한 상황에 의해서 앱에서 수신된 설치 경로 정보를 SDK에 직접 설정하고자 하는 경우에는 다음의 코드를 사용하세요
 
AppDelegate 정의 항목중 didFinishLaunchingWithOptions 함수 정의에서 파라미터로 전달받은 launchOptions 로 부터 설치 경로를 직접 획득후, 
수신된 값을 setInstallReferrer() 함수를 사용해서 SDK로 전달할 수 있습니다
다만, 주의할 사항은 아래와 같이 직접 앱 설치 경로를 SDK에 설정하는 경우,
해당 코드가 매 실행 시점마다 반복해서 실행되지 않고 앱 설치후 최초 1회만 동작하도록 적용되어야 합니다 
또한, 아래의 코드는 반드시 SDK initialization() 함수 호출 이후 에 적용되어야 합니다
 
#### - Objective-C
```Objective-c
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions { 
    NSURL *referrer = [launchOptions valueForKey:UIApplicationLaunchOptionsURLKey];  
    if (referrer){
        [DOT setInstallReferrer:referrer]; 
    } 
}
```

#### - Swift
```Swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool { 
    if let referrer = launchOptions?[.url] as? URL {
        DOT.setInstallReferrer(referrer);  
    } 
}
```

#### <a id="DOT_DEEPLINK"></a> - 외부 유입 경로 분석 (Deeplink)
앱이 설치된 이후 DeepLink를 통해서 앱이 실행되는 경로 분석이 필요한 경우 아래와 같이 **setDeepLink()** 함수를 사용하면 분석이 가능합니다.

#### - Objective-C
```objective-c
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary *)options {
    [DOT setDeepLink:[url absoluteString]];
    return YES;
}
```

#### - Swift
```Swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    DOT.setDeepLink(url.absoluteString)
    return true
}
```

#### - Facebook 분석
Facebook 앱에서 유입되는 설치수를 분석하기 위해서는 Facebook에서 제공하는 SDK가 분석 대상 앱에 설치가 선행되어야 합니다
 
- FBSDK 다운로드 방법

a. XCode 프로젝트 파일중 Podfile 파일에 다음과 같이 SDK를 추가합니다.

```xml
pod 'FacebookSDK' 
```

b. Podfile 에 dependency 를 추가한 뒤에는 Terminal 프로그램을 실행하여 다음의 명령을 수행합니다.

```xml
cmd> pod install
```
만약 Cocoapad 환경의 프로젝트가 아닌 경우에는, 아래의 링크에서 다운로드가 가능합니다.
다운로드한 Framework 파일을 XCode 프로젝트에서 참조 가능하도록 설정 하시기 바랍니다.

[FBSDK iOS 다운로드 하기](https://developers.facebook.com/docs/ios/downloads)


- FBSDK 설치 방법

a. info.plist 파일을 'Source Code'로 오픈합니다
b. 이름 속성 아래에 포함된 내용중 [APP_ID] 와 [APP_NAME] 부분을 Facebook Developer Site 에서 제공하는 값으로 치환후 info.plist 파일에 저장합니다.

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>fb[APP_ID]</string>
        </array>
    </dict>
</array>
<key>FacebookAppID</key>
<string>[APP_ID]</string>
<key>FacebookDisplayName</key>
<string>[APP_NAME]</string>
```

- FBSDK 로부터 Install Referrer를 수신하고, SDK에 전달하는 방법
사용자가 Facebook에 노출된 광고를 클릭하고 앱을 설치한 경우 설치된 앱에서는 FBSDK를 통해서 AppLinkData를 수신받을 수 있습니다.
아래의 코드에서 FBSDK로부터 AppLinkData를 수신 받고, 수신 받은 AppLinkData 를 SDK로 전달하는 방법을 확인할 수 있습니다.
이 함수는 appDelegate 의 didFinishLaunchingWithOptions 함수에 적용하세요.

##### - Objective-C
```Ojective-C
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [FBSDKAppLinkUtility fetchDeferredAppLink:^(NSURL *url, NSError *error) {
        if(error) {
            NSLog(@"Received error while fetching deferred app link %@", error);
        }
        if(url) {
            [DOT setFacebookreferrerData:url];
        }
    }];
} 
```

##### - Swift
```Swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool { 
    FBSDKAppLinkUtility.fetchDeferredAppLink({ url, error in
        if error != nil {
            if let anError = error {
                print("Received error while fetching deferred app link \(anError)")
            }
        }
        if let unwrappedUrl = url {
            DOT.setFacebookreferrerData(unwrappedUrl)
        }
    })
} 
```

#### - 푸시 메시지 분석
푸시 메시지와 관련되어 push token값을 수집하고, 
수신된 메시지를 클릭하여 앱이 실행된 오픈수 분석등을 적용하는 방법에 대해서 설명합니다

- push token 분석 방법
push token은 푸시 발송 시스템에서 메시지 전송시 수신 대상이 되는 개개인 별로 Unique하게 발급되는 일종의 식별ID 값입니다. 
push token을 분석하기 위해서 AppDelegate 에 정의된 didRegisterForRemoteNotificationsWithDeviceToken() 함수에 
아래와 같이 분석 코드를 적용합니다

##### - Objective-C

```Objective-c
- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
    NSString *strDeviceToken = [deviceToken description];
    NSCharacterSet *characterSet = [NSCharacterSet characterSetWithCharactersInString:@"< >"];
    NSString *strWithoutSpace = [[strDeviceToken stringByTrimmingCharactersInSet:characterSet] stringByReplacingOccurrencesOfString:@" " withString:@""];
    [DOT setPushToken:strWithoutSpace];
}
```

##### - Swift

```Swift
func application(_ application: UIApplication,didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
    let tokenParts = deviceToken.map { data -> String in
        return String(format: "%02.2hhx", data)
    }
    let token = tokenParts.joined()
    DOT.setPushToken(token)
}
```

- 수신된 푸시 메시지를 클릭하여 앱이 실행된 오픈수 분석 방법 각 단말기에 수신된 푸시 메시지를 사용자가 클릭한 경우에 대해서 분석합니다. 푸시 메시지를 클릭하고, 실행되는 로직에서 아래의 분석 코드를 적용합니다.

##### - Objective-C

```Objective-c
[DOT setPushClick:userInfo];
```
##### - Swift

```Swift
DOT.setPushClick(userInfo)
```

#### - Universal Link분석
iOS에서 제공하는 Universal Link분석을 위해 universal link로 진입 delegate method에 아래와 같이 추가합니다.

##### - Objective-C
```objective-c
- (BOOL)application:(UIApplication *)application continueUserActivity:(NSUserActivity *)userActivity restorationHandler:(void (^)(NSArray<id<UIUserActivityRestoring>> * _Nullable))restorationHandler {
    [DOT setDeepLink:userActivity.webpageURL.absoluteString];
    
    return false;
}
```

##### - Swift
```Swift
func application(_ application: UIApplication, continue userActivity: NSUserActivity, restorationHandler: @escaping ([UIUserActivityRestoring]?) -> Void) -> Bool {
        
        if let uniLink = userActivity.webpageURL?.absoluteString {
            DOT.setDeepLink(uniLink)
        }
        return false;
    }
}
```

### <a id="DOT_ADVANCE"></a>고급 컨텐츠 분석 (optional)

in-App 에서 발생하는 다양한 이벤트를 분석하기 위해서는 분석 대상 앱에서 해당 이벤트가 발생된 시점에, SDK에게 해당 정보를 전달해야 합니다.
이어지는 내용에서는 주요 이벤트들의 분석 방법에 대해서 자세하게 설명합니다.

#### <a id="DOT_USER"></a> 로그인 분석
- 로그인 이벤트 분석
분석 대상 앱에 로그인 기능이 있는 경우에, 로그인 이벤트에 대한 발생 여부를 분석할 수 있습니다.
로그인 처리 완료후, 로그인 완료 페이지에 아래와 같이 분석 코드를 적용합니다.

##### - Objective-C
```objective-c 
- (void)viewDidAppear:(BOOL)animated {
    [super viewDidAppear:animated];  

    [DOT onStartPage];
    [DOT setPage:
        [Page builder:^(Page *page) {
            [page setPageIdentity:@"LIR"]; 
        }]
    ];
}
```

##### - Swift
```Swift
override func viewDidAppear(_ animated: Bool) {
    super.viewDidAppear(animated)

    DOT.onStartPage()
    DOT.setPage(
        Page.builder({ (builder) in
            let page = builder as! Page
            page.setPageIdentity("LIR")
        })
    )
}
```

만약에 위와 같이 로그인 완료 페이지가 따로 존재하지 않고, 곧 바로 메인화면 또는 컨텐츠 페이지로 이동하는 경우가 있을 수 있습니다. 이 경우에는 로그인을 처리하는 Backend 로직을 수행후, 아래와 같이 로그인 완료 이벤트를 전송할 수 있습니다.

##### Objective-C
```Objective-c 
... 로그인 처리 로직 수행 ...
[DOT onStartPage];
[DOT setPage:
    [Page builder:^(Page *page) {
        [page setIdentity:@"LIR"]; 
    }]
];
```

##### Swift
```Swift
... 로그인 처리 로직 수행 ...
DOT.onStartPage()
DOT.setPage(
    Page.builder({ (builder) in
        let page = builder as! Page
        page.setPageIdentity("LIR")
    })
)
```

- 회원분석
로그인 완료 이벤트 분석시, 현재 로그인한 사용자의 다양한 정보를 분석할 수 있습니다.
로그인 완료에 대한 처리 완료후, 아래와 같이 분석 코드를 적용합니다.

##### - Objective-C
```objective-c 
[DOT setUser:
    [User builder:^(User *user) {
        [user setGender:@"M"];
        [user setAge:@"A"];
        [user setAttr1:@"attr1"];
    }]
];
```

##### - Swift
```Swift
DOT.setUser(
    User.builder({ (builder) in
        let user = builder as! User 
        user.setGender("M")
        user.setAge("A")
        user.setAttr1("attr1") 
    })
)
```

회원 분석과 관련되어 제공되는 분석 항목은 다음과 같습니다
**\<User Class>**

| Class 이름 | Method 이름 | 파라미터 |
| :------: | :------: | :------: |
| User | setMember(isMember) |  회원여부를 나타내는 Y,N값 전달 |
| User | setMemberGrade(grade) | 회원등급을 나타내는 코드값 전달 |
| User | setMemberId(userId) | 회원의 로그인 아이디를 전달 |
| User | setGender(gender) | 회원 성별을 나타내는 M,F,U 중 한가지 값 전달 |
| User | setAge(age) | 회원 연령을 나타내는 코드값 전달 |
| User | setAttr1(attr) | 회원 속성#1 의미하는 코드값 전달 |
| User | setAttr2(attr) | 회원 속성#2 의미하는 코드값 전달 |
| User | setAttr3(attr) | 회원 속성#3 의미하는 코드값 전달 |
| User | setAttr4(attr) | 회원 속성#4 의미하는 코드값 전달 |
| User | setAttr5(attr) | 회원 속성#5 의미하는 코드값 전달 |

#### in-App 분석

- 상품 페이지 분석 : e-commerce 앱의 경우 상품 상세 페이지에 분석코드를 적용하여, 상품별 조회수를 분석합니다. 상품 상세 페이지에 아래와 같이 분석 코드를 적용하세요.

##### - Objective-C
```Objective-C
- (void)viewDidAppear:(BOOL)animated {
    [super viewDidAppear:animated];  

    [DOT onStartPage];
    [DOT setPage:
        [Page builder:^(Page *page) {
            [page setPageIdentity:@"PDV"]; 
            page.product = [Product builder:^(Product *product) {
                [product setFirstCategory:@"상품카테고리(대)"];
                [product setProductCode:@"상품코드"];
                [product setAttr1:@"상품속성#1"];
            }];

        }]
    ];
}
```

##### - Swift
```Swift
override func viewDidAppear(_ animated: Bool) {
    super.viewDidAppear(animated)

    DOT.onStartPage()
    DOT.setPage(
        Page.builder({ (builder) in
            let page = builder as! Page
            page.setPageIdentity("PDV")
            page.product = Product.builder({ (builder) in
                let product = builder as! Product
                product.setFirstCategory("상품카테고리(대)")
                product.setProductCode("상품코드")
                product.setAttr1("상품속성#1")
            })
        })
    )
} 
```

상품 페이지 분석과 관련되어 제공되는 상품 분석 항목은 다음과 같습니다
**\<Product Class>**
        
| Class 이름 | Method 이름 | 파라미터 |
| :-----: | :-----: | :-----: |
| Product | setFirstCategory(value) |  상품 카테고리(대) 값을 전달 |
| Product | setSecondCategory(value) | 상품 카테고리(중) 값을 전달 |
 | Product | setThirdCategory(value)| 상품 카테고리(소) 값을 전달 |
| Product | setDetailCategory(value) | 상품 카테고리(세) 값을 전달 |
| Product | setProductCode(value) | 상품 코드 값을 전달 |
| Product | setAttr1(attr) | 회원 속성#1 의미하는 코드값 전달 |
| Product | setAttr2(attr) | 회원 속성#2 의미하는 코드값 전달 |
| Product | setAttr3(attr) | 회원 속성#3 의미하는 코드값 전달 |
| Product | setAttr4(attr) | 회원 속성#4 의미하는 코드값 전달 |
| Product | setAttr5(attr) | 회원 속성#5 의미하는 코드값 전달 |
| Product | setAttr6(attr) | 회원 속성#6 의미하는 코드값 전달 |
| Product | setAttr7(attr) | 회원 속성#7 의미하는 코드값 전달 |
| Product | setAttr8(attr) | 회원 속성#8 의미하는 코드값 전달 |
| Product | setAttr9(attr) | 회원 속성#9 의미하는 코드값 전달 |
| Product | setAttr10(attr) | 회원 속성#10 의미하는 코드값 전달 |

- Page Identiy 분석 : 앱에 존재하는 각 페이지를 의미하는 Identity를 사용자가 정의하고, 각 화면들에 정의된 Identity를 적용하면, 앱에서 가장 사용 빈도가 높은 화면별 랭킹을 알 수 있습니다.
**\*Identity값은 AlphaNumeric 형태를 가지는 최대길이 8자 미만의 코드값 이어야 합니다.**

##### - Objective-C
```Objective-C
- (void)viewDidAppear:(BOOL)animated {
    [super viewDidAppear:animated];  

    [DOT onStartPage];
    [DOT setPage:
        [Page builder:^(Page *page) {
            [page setIdentity:@"Your Page Identity Value"]; 
        }]
    ];
}
```

##### - Swift
```Swift
override func viewDidAppear(_ animated: Bool) {
    super.viewDidAppear(animated)

    DOT.onStartPage()
    DOT.setPage(
        Page.builder({ (builder) in
            let page = builder as! Page
            page.setPageIdentity("Your Page Identity Value")
        })
    )
}
```

- Contents Path 분석 : 앱에 존재하는 각 페이지에 Hierarchical 한 Contents Path값을 적용하면, 각 컨텐츠의 사용 비율을 카테고리별로 그룹화 하여 분석이 가능합니다.**Contents Path는 '^' 문자를 구분자**로 하고, **Contents Path의 시작은 ^ 문자로 시작** 되어야 합니다. **Contents Path로 전달되는 값에는 ' 와 " 기호는 사용할 수 없습니다.**

##### - Objective-C
```Objective-C
- (void)viewDidAppear:(BOOL)animated {
    [super viewDidAppear:animated];  

    [DOT onStartPage];
    [DOT setPage:
        [Page builder:^(Page *page) {
            [page setContentPath:@"^메인^계정정보 수정"]; 
        }]
    ];
}
```

##### - Swift

```Swift
override func viewDidAppear(_ animated: Bool) {
    super.viewDidAppear(animated)

    DOT.onStartPage()
    DOT.setPage(
        Page.builder({ (builder) in
            let page = builder as! Page
            page.setContentsPath("^메인^계정정보 수정")
        })
    )
}
```

- Multi Variables 분석 (사용자 정의 변수) : Multi Variables 분석 항목은 사용자가 그 항목에 전달할 값을 정의하여 사용이 가능합니다.비즈니스에서 필요한 분석 항목을 SDK API 함수로 전달하고, 그렇게 전달된 값을 기준으로 페이지뷰수, 방문수 등을 측정하고 보여줍니다. **Multi Variables 의 분석값은 값에는 ' 와 " 기호는 사용할 수 없습니다 ( 영어,숫자,한글만 사용 가능 )**

##### - Objective-C
```Objective-C
- (void)viewDidAppear:(BOOL)animated {
    [super viewDidAppear:animated];  

    [DOT onStartPage];
    [DOT setPage:
        [Page builder:^(Page *page) { 
            page.customValue = [CustomValue builder:^(CustomValue *customValue) {
                [customValue setValue1:@"Multi Variables 값"];
            }]; 
        }]
    ];
}
```

##### - Swift
```Swift
override func viewDidAppear(_ animated: Bool) {
    super.viewDidAppear(animated)
    DOT.onStartPage()
    DOT.setPage(
        Page.builder({ (builder) in
            let page = builder as! Page
            page.setContentsPath("^메인^계정정보 수정")
            page.customValue = CustomValue.builder({ (builder) in
            let customValue = builder as! CustomValue
                customValue.setValue1("Multi Variables 값")
            })
        })
    )
}
```

**\<CustomValue Class>**
        
| Class 이름 |  Method 이름 | 파라미터 | 
| :-----: |  :-----: | :-----: | 
| CustomValue | setValue1(value) | Multi Variables#1 값을 전달 | 
| CustomValue | setValue2(value) | Multi Variables#2 값을 전달 | 
| CustomValue | setValue3(value) | Multi Variables#3 값을 전달 | 
| CustomValue | setValue4(value) | Multi Variables#4 값을 전달 | 
| CustomValue | setValue5(value) | Multi Variables#5 값을 전달 | 
| CustomValue | setValue6(value) | Multi Variables#6 값을 전달 | 
| CustomValue | setValue7(value) | Multi Variables#7 값을 전달 | 
| CustomValue | setValue8(value) | Multi Variables#8 값을 전달 | 
| CustomValue | setValue9(value) | Multi Variables#9 값을 전달 | 
| CustomValue | setValue10(value) | Multi Variables#10 값을 전달 | 

- 내부 검색어 분석 :앱에 검색기능이 있는 경우, 사용자가 입력한 검색어와, 검색한 카테고리, 검색 결과수등을 분석하면, 검색 기능의 활용성을 측정할 수 있습니다. 검색 결과가 보여지는 화면에 분석 코드를 적용합니다.

##### - Objective-C
```Objective-C
- (void)viewDidAppear:(BOOL)animated {
    [super viewDidAppear:animated];

    [DOT onStartPage];
    [DOT setPage:
        [Page builder:^(Page *page) {
            // 사용자가 통합 검색 카테고리에서 청바지 검색어로 1200개의 검색 결과를 보았을떄 적용 예시
            [page setSearchingResult:1200];
            [page setKeywordCategory:@"통합검색"];
            [page setKeyword:@"청바지"];
        }]
    ];
}
```

##### - Swift

```Swift
override func viewDidAppear(_ animated: Bool) {
    super.viewDidAppear(animated)

    DOT.onStartPage()
    // 사용자가 통합 검색 카테고리에서 청바지 검색어로 1200개의 검색 결과를 보았을떄 적용 예시
    DOT.setPage(
        Page.builder({ (builder) in
            let page = builder as! Page
            page.setSearchResultCount(1200)
            page.setKeywordCategory("통합검색")
            page.setKeyword("청바지") 
        })
    )
}
```

- 검색 결과 클릭 분석 : 검색 결과 페이지에서 보여지는 많은 검색 결과 항목별 클릭수를 분석합니다. 이 분석 결과를 통해서 검색 결과의 상단에 노출되는 항목들이 적절한지 가늠할 수 있습니다. 검색 결과 페이지에서 특정 항목이 클릭되면, 해당 화면으로 이동하기 이전에 아래와 같이 분석 코드를 적용하세요.

##### - Objective-C
```objective-c
DOT.setClick(
    new Click.Builder() 
    .setSearchClickEvent("클릭된 검색 결과 항목 ID") 
    .build()
);
```

##### - Swift
```Swift
DOT.setClick(
    Click.builder({ (builder) in
        let click = builder as! Click
        click.setSearchClickEvent("클릭된 검색 결과 항목 ID") 
    })
)
```

- 장바구니 담긴 상품 분석 : e-commerce 관련된 비즈니스의 경우 장바구니에 담긴 상품을 분석할 수 있습니다. 장바구니 담기 이벤트 발생시 아래와 같은 코드를 적용하세요.

##### - Objective-C
```Objective-C
[DOT setClick:
    [Click builder:^(Click *click) {
        [click addCartProduct:[Product builder:^(Product *product) {
                [product setFirstCategory:@"상품카테고리(대)"];
                [product setProductCode:@"상품코드"];
                [product setAttr1:@"상품속성#1"]; 
            }]
        ];
    }]
];
```

##### - Swift
```Swift
DOT.setClick(
    Click.builder({ (builder) in
        let click = builder as! Click
        click.addCartProduct(Product.builder({ (builder) in
            let product = builder as! Product
            product.setFirstCategory("상품카테고리(대)")
            product.setProductCode("상품코드")
            product.setAttr1("상품속성#1")
        }))
    })
)
```

- 클릭 이벤트 분석 : 앱에 존재하는 다양한 클릭 요소 (배너, 버튼 등)에 대해서, 클릭수를 분석합니다. 각 요소가 클릭되는 시점에 아래와 클릭된 요소의 목적지 화면으로 이동하기 이전에 아래와 같은 분석 코드를 적용하세요.

##### - Objective-C
```Objective-C
[DOT setClick:
    [Click builder:^(Click *click) {
        [click setClickEvent:@"클릭 요소 ID"]; 
    }]
];
```

##### - Swift
```Swift
DOT.setClick(
    Click.builder({ (builder) in
        let click = builder as! Click
        click.setClickEvent("클릭 요소 ID")  
    })
)
```

**\*클릭된 요소의 ID값으로 단일 문자열로된 값을 전달하기도 하지만, 앞에서 설명한 Contents Path 분석 과 같이, Hierarchical 한 Path값을 전달하여 추후 데이터 조회시 Categorizing 하게 보기도 가능합니다. Hierarchical 한 Path 값을 사용하고자 할때 값에 대한 제약사항은 Contents Path 분석 과 동일합니다.**

- 클릭 이벤트 고급 분석(Multi Variables) : 클릭 이벤트 분석시 앞에서 설명한 Multi Variables 분석 을 같이 적용하면, Multi Variables 분석 항목별 클릭수 를 측정할 수 있습니다. 클릭 이벤트가 발생된 시점에 다음과 같이 Multi Variables 값을 같이 SDK에 전달하도록 분석코드를 적용하세요.

##### - Objective-C
```Objective-C
// 클릭 이벤트 분석시 Multi Variables 분석값을 같이 전송하는 예시
[DOT setClick:
    [Click builder:^(Click *click) {
        [click setClickEvent:@"클릭 요소 ID"]; 
        click.customValue = [CustomValue builder:^(CustomValue *customValue) {
            [customValue setValue1:@"Multi Variables 값"];
        }];
    }]
];
```

##### - Swift
```Swift
// 클릭 이벤트 분석시 Multi Variables 분석값을 같이 전송하는 예시
DOT.setClick(
    Click.builder({ (builder) in
        let click = builder as! Click
        click.setClickEvent("클릭 요소 ID")
        click.customValue = CustomValue.builder({ (builder) in
            let customValue = builder as! CustomValue
            customValue.setValue1("Multi Variables 값")
        })
    })
)
```

Multi Variables 분석과 관련되어 제공되는 분석 항목은 다음과 같습니다

|Class 이름|Method 이름|파라미터|
|:--:|:--:|:--:|
|CustomValue|setValue1(value)|Multi Variables#1 값을 전달|
|CustomValue|setValue2(value)|Multi Variables#2 값을 전달|
|CustomValue|setValue3(value)|Multi Variables#3 값을 전달|
|CustomValue|setValue4(value)|Multi Variables#4 값을 전달|
|CustomValue|setValue5(value)|Multi Variables#5 값을 전달|
|CustomValue|setValue6(value)|Multi Variables#6 값을 전달|
|CustomValue|setValue7(value)|Multi Variables#7 값을 전달|
|CustomValue|setValue8(value)|Multi Variables#8 값을 전달|
|CustomValue|setValue9(value)|Multi Variables#9 값을 전달|
|CustomValue|setValue10(value)|Multi Variables#10 값을 전달|


#### Micro Conversion 분석
- Conversion 분석
앱내에 존재하는 Conversion중 가장 대표적인게 **구매 전환** 을 생각할 수 있습니다. 하지만, 앱내에는 앱이 제공하는 서비스에 따라서 매우 다양한 Conversion이 존재할 수 있습니다. 또한, 이미 정의된 Conversion 일지라도, 서비스의 변화, 시대의 변화애 따라서 새로 정의되어야 하기도 하고, 사용하지 않아서 폐기되기도 합니다.
SDK는 총 80개의 Conversion을 사용자가 정의하고, 분석 코드를 적용함으로써 앱으로 인하여 발생하는 Conversion 측정이 가능합니다. 이는 **구매 전환과는 독립적으로 분석되며, 사용자는 언제든지 분석 코드의 적용 기준을 새로 정의할 수 있습니다.**

##### - Objective-C
```Objective-C
// Micro Conversion #1 번의 사용 예시
[DOT setConversion:
    [Conversion builder:^(Conversion *conversion) {
        [conversion setMicroConversion1:1]; 
    }];
];
```

##### - Swift
```Swift
// Micro Conversion #1 번의 사용 예시
DOT.setConversion(
    Conversion.builder { (builder) in
        let conversion = builder as! Conversion
        conversion.setMicroConversion1(1)
    }
)
```

- Conversion 고급 분석(상품) 
Conversion은 단순하게 발생 횟수를 측정할 수도 있으나, 상품과 연계하여 상품별로 정의한 Conversion의 발생 횟수 측정도 가능합니다. 이벤트가 발생한 시점에 아래와 같이 Conversion Data + Product Data 를 SDK로 전달하세요.
 
##### - Objective-C
```Objective-C
[DOT setConversion:
    [Conversion builder:^(Conversion *conversion) {
        [conversion setMicroConversion1:1];
        conversion.product = [Product builder:^(Product *product) {
            [product setFirstCategory:@"상품카테고리(대)"];
            [product setProductCode:@"상품코드"];
            [product setAttr1:@"상품속성#1"];
        }];
    }];
];
```

##### - Swift
```Swift
DOT.setConversion(
    Conversion.builder { (builder) in
        let conversion = builder as! Conversion
        conversion.setMicroConversion1(1)
        conversion.product = Product.builder({ (builder) in
            let product = builder as! Product
            product.setFirstCategory("상품카테고리(대)")
            product.setProductCode("상품코드")
            product.setAttr1("상품속성#1")
        })
    }
)

Conversion 고급 분석( 상품 )과 관련되어 제공되는 상품 분석 항목은 다음과 같습니다

|Class 이름|Method 이름|파라미터|
|:--:|:--:|:--:|
|Product|setFirstCategory(value)|상품 카테고리(대) 값을 전달|
|Product|setSecondCategory(value)|상품 카테고리(중) 값을 전달|
|Product|setThirdCategory(value)|상품 카테고리(소) 값을 전달|
|Product|setDetailCategory(value)|상품 카테고리(세) 값을 전달|
|Product|setProductCode(value)|상품 코드 값을 전달|
|Product|setAttr1(value)|상품 속성 #1 값을 전달|
|Product|setAttr2(value)|상품 속성 #2 값을 전달|
|Product|setAttr3(value)|상품 속성 #3 값을 전달|
|Product|setAttr4(value)|상품 속성 #4 값을 전달|
|Product|setAttr5(value)|상품 속성 #5 값을 전달|
|Product|setAttr6(value)|상품 속성 #6 값을 전달|
|Product|setAttr7(value)|상품 속성 #7 값을 전달|
|Product|setAttr8(value)|상품 속성 #8 값을 전달|
|Product|setAttr9(value)|상품 속성 #9 값을 전달|
|Product|setAttr10(value)|상품 속성 #10 값을 전달|



- Conversion 고급 분석( Multi Variables )
 Multi Variables 항목과 연계하여 Conversion의 발생 횟수 측정도 가능합니다. 이벤트가 발생한 시점에 아래와 같이 Conversion Data + Multi Variables Data를 SDK로 전달하세요.


##### - Objective-C
```Objective-C
[DOT setConversion:
    [Conversion builder:^(Conversion *conversion) {
        [conversion setMicroConversion1:1];
        conversion.customValue = [CustomValue builder:^(CustomValue *customValue) {
            [customValue setValue1:@"Multi Variables 값"];
        }];
    }];
];
```

##### - Swift
```Swift
DOT.setConversion(
    Conversion.builder { (builder) in
        let conversion = builder as! Conversion
        conversion.setMicroConversion1(1)
         conversion.customValue = CustomValue.builder({ builder in
            let customValue = builder as! CustomValue
            customValue.setValue1("Multi Variables 값")
        })
    }
)
```

Conversion 고급 분석( Multi Variables )과 관련되어 제공되는 분석 항목은 다음과 같습니다

|Class 이름|Method 이름|파라미터|
|:--:|:--:|:--:|
|CustomValue|setValue1(value)|Multi Variables#1 값을 전달|
|CustomValue|setValue2(value)|Multi Variables#2 값을 전달|
|CustomValue|setValue3(value)|Multi Variables#3 값을 전달|
|CustomValue|setValue4(value)|Multi Variables#4 값을 전달|
|CustomValue|setValue5(value)|Multi Variables#5 값을 전달|
|CustomValue|setValue6(value)|Multi Variables#6 값을 전달|
|CustomValue|setValue7(value)|Multi Variables#7 값을 전달|
|CustomValue|setValue8(value)|Multi Variables#8 값을 전달|
|CustomValue|setValue9(value)|Multi Variables#9 값을 전달|
|CustomValue|setValue10(value)|Multi Variables#10 값을 전달|



#### Purchase 분석
- Purchase 분석

앱내에서 발생하는 구매 이벤트를 분석합니다. 구매 완료 페이지에서 아래와 같이 구매와 관련된 정보를 SDK에 전달하세요.

##### - Objective-C

```Objective-C
[DOT setPage:
    [Page builder:^(Page *page) {
        [page setPageIdentity:@"ODR"]; 
    }]
];
[DOT setPurchase:
    [Purchase builder:^(Purchase *purchase) {
        [purchase setOrderNo:@"Your Order Number"];
        [purchase setCurrency:KRW"];
        purchase.orderProduct = [Product builder:^(Product *product) {
            [product setFirstCategory:@"상품카테고리(대)"];
            [product setProductCode:@"상품코드"];
            [product setOrderAmount:10000];
            [product setOrderQuantity:1];
        }];
    }]
];
```

##### - Swift

```Swift
DOT.setPurchase(
    Purchase.builder({ (builder) in
        let purchase = builder as! Purchase
        purchase.setOrderNo("Your Order Number")
        purchase.setCurrency("KRW")
        purchase.orderProduct = Product.builder({ (builder) in
            let product = builder as! Product
            product.setFirstCategory("상품카테고리(대)") 
            product.setProductCode("상품코드")
            product.setOrderAmount(10000)
            product.setOrderQuantity(1)
        })
    })
)
```
Purchase 분석과 관련되어 제공되는 분석 항목은 다음과 같습니다

|Class 이름|Method 이름|파라미터|
|:--:|:--:|:--:|
|Purchase|setOrderNumber(value)|구매 이벤트에 대한 통합 주문번호 값을 전달|
|Purchase|setCurrency(value)|결제된 금액의 화폐 기준을 전달|
|Product|setFirstCategory(value)|상품 카테고리(대) 값을 전달|
|Product|setSecondCategory(value)|상품 카테고리(중) 값을 전달|
|Product|setThirdCategory(value)|상품 카테고리(소) 값을 전달|
|Product|setDetailCategory(value)|상품 카테고리(세) 값을 전달|
|Product|setProductCode(value)|상품 코드 값을 전달|
|Product|setAttr1(value)|상품 속성 #1 값을 전달|
|Product|setAttr2(value)|상품 속성 #2 값을 전달|
|Product|setAttr3(value)|상품 속성 #3 값을 전달|
|Product|setAttr4(value)|상품 속성 #4 값을 전달|
|Product|setAttr5(value)|상품 속성 #5 값을 전달|
|Product|setAttr6(value)|상품 속성 #6 값을 전달|
|Product|setAttr7(value)|상품 속성 #7 값을 전달|
|Product|setAttr8(value)|상품 속성 #8 값을 전달|
|Product|setAttr9(value)|상품 속성 #9 값을 전달|
|Product|setAttr10(value)|상품 속성 #10 값을 전달|
|Product|setProductOrderNumber(value)|상품별 주문번호 전달|
|Product|setOrderAmount(value)|상품별 구매금액을 전달|
|Product|setOrderQuantity(value)|상품별 구매 수량을 전달|
|Product|setRefundAmount(value)|상품별 환불 금액을 전달|
|Product|setRefundQuantity(value)|상품별 환불 수량을 전달|

- Purchase 고급 분석( Multi Variables ) 

 Multi Variables 항목과 연계하여 Purchase 분석도 가능합니다. 이벤트가 발생한 시점에 아래와 같이 Purchase Data + Multi Variables Data 를 SDK로 전달하세요.

##### - Objective-C
```Objective-C
[DOT setPurchase:
    [Purchase builder:^(Purchase *purchase) {
        [purchase setOrderNo:@"Your Order Number"];
        [purchase setCurrency:KRW"];
        purchase.orderProduct = [Product builder:^(Product *product) {
            [product setFirstCategory:@"상품카테고리(대)"];
            [product setProductCode:@"상품코드"];
            [product setOrderAmount:10000];
            [product setOrderQuantity:1];
        }];
        purchase.customValue = [CustomValue builder:^(CustomValue *customValue) {
            [customValue setValue1:@"Multi Variables 값"];
        }];
    }]
];
```

##### - Swift
```Swift
DOT.setPage(
    Page.builder({ (builder) in
        let page = builder as! Page
        page.setPageIdentity("ODR")
    })
)
DOT.setPurchase(
    Purchase.builder({ (builder) in
        let purchase = builder as! Purchase
        purchase.setOrderNo("Your Order Number")
        purchase.setCurrency("KRW")
        purchase.orderProduct = Product.builder({ (builder) in
            let product = builder as! Product
            product.setFirstCategory("상품카테고리(대)") 
            product.setProductCode("상품코드")
            product.setOrderAmount(10000)
            product.setOrderQuantity(1)
        })
        purchase.customValue = CustomValue.builder({ (builder) in
            let customValue = builder as! CustomValue
            customValue.setValue1("Multi Variables 값")
        })
    })
)
```


	
