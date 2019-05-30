# SDK Implementation guide(iOS) 


## SDK 적용 방법

### 1. SDK Installation
Add the three framework files provided below.

TARGETS - General - Embedded Binaries

![DOX.logEvent(XEvent)](./DOX_SDK_Install.png)<br/>

<!--프로젝트 네이게이터에서 아래와 같은 상태를 확인할 수 있습니다.

<img src="DOX_SDK_Install2.png " width="200">-->

### 2. Key 등록
Add the App Analytics Key information provided in the info.plist file of the XCode project
When you open info.plist file, open it as 'Source Code' instead of 'Property list' and then press Ctrl + C/V to get the key you provided.
The key value provided is data having xml form as shown in the example below.

```xml
<key>dotAuthorizationKey</key>
<dict>
		<key>domain_x</key>
		<string>http://report.wisetracker.co.kr/gsshop.html</string>
		<key>useMode</key>
		<integer>3</integer>
		<key>accessToken</key>
		<string></string>
		<key>expireDate</key>
		<string>14</string>
		<key>isDebug</key>
		<string>true</string>
		<key>isFingerPrint</key>
		<string>true</string>
		<key>isInstallRetention</key>
		<string>true</string>
		<key>serviceNumber</key>
		<string>103</string>
</dict>
```

To allow http communication, add NSAppTransportSecurity as follows.

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

### 3. SDK initialization
Apply the code for initializing the SDK to the ** didFinishLaunchingWithOptions ** function of the class in which the AppDelegate of the XCode project is defined.

```Swift
import DOX
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
      
        DOX.initialization()
        return true
    }
```
    
```Objective-c
#import <DOX/DOX.h>
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    [DOX initialization];
    
    return true;
}
```


Where DOX is used, import is required via import DOX and #import <DOX / DOX.h> in Swift and Objecitve-C, respectively.
In the following example, import is omitted for readability.

## 유입경로 분석

### 외부 유입 경로 분석(DeepLink)

If you need to analyze the path that your app runs through DeepLink since the app is installed, you can use the setDeepLink () function as below.

#### - Swift
```Swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        DOX.setDeepLink(url.absoluteString)
        return true
}
```

#### - Objective-C
```Objective-C
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options {
    [DOT setDeepLink:[url absoluteString]];
    return YES;
}
```

### 푸시 분석

Analyze when a user clicks on a push message received at each terminal. Apply the following analytics code at the time you click the push message.

#### - Swift
```Swift
   DOX.setPushId("P12342345234")
```

#### - Objective-C
```Objective-C
	[DOX setPushId:@"pushID"];
```

## InApp Analysis
### 1. logEvent() 
The logEvent() is used when you want to send various event data (page visit, click event, etc.) occurred from a web page. It has the following usage relationship.<br/><br/>
![DOX.logEvent(XEvent)](https://raw.githubusercontent.com/xsightin/dop-website-sdk/master/document/Implementation/logEvent.png?token=AJT3SWREBFLTSQDJQCRHLSS45TWWA)<br/>

The logEvent() receives the XEvent as a parameter.
The setEventName() of the XEvent is required property.
If necessary, you can use the setProperties() of the XEvent to add custom data related to the event. <br/>

#### - Swift

```Swift
// example 1
    DOX.logEvent(XEvent.builder({ (builder) in
        let event = builder as! XEvent
        event.setEventName("myPage")
    }))
);

// example 2 
    DOX.logEvent(XEvent.builder({ (builder) in
        let event = builder as! XEvent
        event.setEventName("my page with some data")
        event.setProperties(XProperties.builder({ (builder) in
            let properties = builder as! XProperties
            properties.set("pageId", value: "MAIN")
        }))
    }))
```

#### - Objective-C
```Objective-C
// example 1
    [DOX logEvent:[XEvent builder:^(XEvent *event) {
            [event setEventName:@"mypage"];
    }]];
  
// example 2
 [DOX logEvent:[XEvent builder:^(XEvent *event) {
    [event setEventName:@"my page with some data"];
    [event setProperties:[XProperties builder:^(XProperties *properties) {
        [properties set:@"pageId" value:@"MAIN"];
    }]];
 }]];
```   


### 2. logConversion() 
The logConversion() is used when you want to send a micro-conversion event that has an analytic meaning among the events that occur on a web page, and it has the following usage relationship.<br/><br/>
![DOX.logConversion(XConversion)](https://raw.githubusercontent.com/xsightin/dop-website-sdk/master/document/Implementation/logConversion.png?token=AJT3SWXO6JQIAVEQMH3NV3K45TWTY)<br/>

The logConversion() receives the XConversion as a parameter, The setConversionName() of the XConversion is required property.
If necessary, you can use the setProperties() function of XConversion to add custom data related to conversion. <br/>

#### - Swift
```Swift
// example 1
   DOX.logConversion(XConversion.builder({ (builder) in
        let conversion = builder as! XConversion
        conversion.setConversionName("start tutorial")
    }))
    
// example 2
    DOX.logConversion(XConversion.builder({ (builder) in
        let conversion = builder as! XConversion
        conversion.setConversionName("start tutorial")
        conversion.setProperties(XProperties.builder({ (builder) in
            let properties = builder as! XProperties
            properties.set("pageId", value:"tutorialStart")
            properties.set("Object", value:["test":"value"])
            properties.set("Array", value: [1,2,3,4])
        }))
    }))    
```

#### - Objective-C
```Objective-C
// example 1
    [DOX logConversion:[XConversion builder:^(XConversion *conversion) {
        [conversion setConversionName:@"start tutorial"];
    }]];

// example 2 
    [DOX logConversion:[XConversion builder:^(XConversion *conversion) {
        [conversion setConversionName:@"start tutorial"];
        [conversion setProperties:[XProperties builder:^(XProperties *properties) {
            [properties set:@"pageId" value:@"tutorialStart"];
            [properties set:@"Object" value:@{@"test":@"value"}];
            [properties set:@"Array" value:@[@1,@2,@3,@4]];
        }]];
    }]];
```
### 3. logRevenue()
The logRevenue() is used when you want to send a purchase event that occurs on a web page and it has the following usage relationship.<br/><br/>
![DOX.logRevenue(XRevenue)](https://raw.githubusercontent.com/xsightin/dop-website-sdk/master/document/Implementation/logRevenue.png?token=AJT3SWUHFGC6QPWRAIE5CD245TWRA)<br/>

The logRevenue() receives the XRevenue as a parameter, The items below of XRevenue are required property.<br/>

- setOrderNo(): Sets the order number associated with the purchase.
- setEventType(): Sets a value that can distinguish whether the event that occurred is a purchase or a refund.
- setCurrency(): Sets the currency code used to process the payment.
- setProduct(), setProductList(): Sets the purchased product information.

Next, when using the setProduct() and setProductList() of XRevenue receives XProduct as a parameter, and the following items of XProduct are required property.<br/>

- setFirstCategory(): Sets the first category information for the product. 
- setProductCode(): Sets the product code that identifies the product.
- setOrderAmount(): Sets the total amount of goods purchased. (Unit price * quantity)
- setOrderQuantity(): Sets the number of goods purchased.
  
If necessary, you can use the setProperties () function of XProduct to add custom data related to each item purchased. <br/><br/>
  
#### - Swift
```Swift
// example 1
    let products = NSMutableArray.builder({ (builder) in
        let products = builder as! NSMutableArray
        let product1 = XProduct.builder({ (builder) in
            let product = builder as! XProduct
            product.setFirstCategory("CAT1")
            product.setSecondCategory("CAT2")
            product.setThirdCategory("CAT3")
            product.setDetailCategory("CAT4")
            product.setProductCode("product_code1")
            product.setOrderQuantity(2)
            product.setOrderAmount(10000)
            product.setProperties(XProperties.builder({ (builder) in
                let properties = builder as! XProperties
                properties.set("isSale", value: "50%")
    
            }))
        })
        products.add(product1 as Any)
    })
    
    DOX.logRevenue(XRevenue.builder({ (builder) in
        let revenue = builder as! XRevenue
        revenue.setCurcy("KRW")
        revenue.setEventType("purchase")
        revenue.setOrdNo("new_order_number_1")
        revenue.setProductList(products!)
  
    }))
```

#### - Objective-c
```
NSMutableArray *products = [NSMutableArray builder:^(NSMutableArray *products) {
        XProduct *product = [XProduct builder:^(XProduct *product) {
            [product setFirstCategory:@"CAT1"];
            [product setSecondCategory:@"CAT2"];
            [product setThirdCategory:@"CAT3"];
            [product setDetailCategory:@"CAT4"];
            [product setProductCode:@"product_code1"];
            [product setOrderQuantity:2];
            [product setOrderAmount:10000];
            [product setProperties:[XProperties builder:^(XProperties *properties) {
                [properties set:@"isSale" value:@"50%"];
            }]];
        }];
        [products addObject:product];
    }];
   
    [DOX logRevenue:[XRevenue builder:^(XRevenue *revenue) {
        [revenue setCurcy:@"KRW"];
        [revenue setEventType:@"purchase"];
        [revenue setOrdNo:@"new_order_number_1"];
        [revenue setProductList:products];
    }]];
```

### 4. setUserId() 
If the login event occurs on the website with the SDK applied, you can set the LOGIN-ID into the SDK.
The LOGIN-ID value is stored in the local storage by the SDK and is transmitted together with the stored ID information when all subsequent events are transmitted.
If the service is expected to log in and log out frequently on the same client device, We recommend resetting the setUserId () function when a logout event occurs in order to avoid analytic distortion.

#### - Swift
```Swift

// 로그인 이벤트 발생
DOX.setUserId("CURRENT_USER_ID");

// 로그아웃 이벤트 발생 
DOX.setUserId("");

``` 

#### - Objective-C
```Objective-C
// 로그인 이벤트 발생
[DOX setUserId:@"CURRENT_USER_ID"];
   
// 로그아웃 이벤트 발생 
[DOX setUserId:@""];
``` 

<hr/>

### What is the Command-Type API?
The SDK can transfer the data occurred by the client to the server and specify the processing method for the data.
This processing is divided into user and group, and the supported command types are as follows.

1. set( String key, Object value )
The server-side processing method for value data transmitted as key is INSERT or UPDATE.
2. setOnce( String key, Object value )
The server-side processing method ONLY INSERT for value data transmitted as key.
This means that the value is set only if the value does not exist, and the value passed is ignored if the value is already set.
3. unset( String key )
The server-side processing method for value data transmitted as key is DELETE.
4. add( String key, int increment )
The server-side processing method for value data transmitted as key is ADD(+).If necessary, you can expect a MINUS(-) processing effect by sending a negative number.
5. append( String key, Object value )
The server-side processing method for value data transmitted as key is JOIN(last append).
If the server-side data is not an Array type, change the original data to array type, data would be appended into the origin.
6. prepend( String key, Object value )
The server-side processing method for value data transmitted as key is JOIN(first insert).
If the server-side data is not an Array type, change the original data to array type, data would be inserted into the origin.

 
### 5. userIdentify() 
The userIdentify() is used when you want to use user-based Command Type API and has the following usage relationships.<br/><br/>
![DOX.userIdentify(XIdentify)](https://raw.githubusercontent.com/xsightin/dop-website-sdk/master/document/Implementation/userIdentify.png?token=AJT3SWX5PF36DXPX5AHCAD245TWMW)<br/>

The userIdentify() receives XIdentify as a parameter.
One or more of the Command APIs provided in the XIdentify must be configured.

#### Swift
```Swift
    DOX.userIdentify(XIdentify.builder({ (builder) in
        let identify = builder as! XIdentify
        identify.setOnce("ID", value: "MRCM")
        identify.set("action", value:"loginSuccess")
        identify.add("visitCount", increment: 1)
    }))
``` 

#### Objective-C
```Objective-C
    [DOX userIdentify:[XIdentify builder:^(XIdentify *identify) {
        [identify setOnce:@"ID" value:@"MRCM"];
        [identify setOnce:@"action" value:@"loginSuccess"];
        [identify add:@"visitCount" increment:1];
    }]];
``` 

### 6. groupIdentify()
The groupIdentify() is used when you want to use group-based Command Type API and has the following usage relationships.<br/><br/>
![DOX.groupIdentify(key, name, XIdentify)](https://raw.githubusercontent.com/xsightin/dop-website-sdk/master/document/Implementation/groupIdentify.png?token=AJT3SWW2XIAEKVUIIBJRO4S45TWFS)<br/>

The groupIdentify() receives three parameter values, each entry has the following values.
 <br/>

- key: Pass an identification code used to identify the group. 
- value: Pass the value for the passed group identification code. 
- XIdentify: You can use the CommandLine API.

One or more of the Command APIs provided in the XIdentify must be configured.

#### Swift
```Swift
    DOX.groupIdentify("company", value: "gsshop", identify: XIdentify.builder({ (builder) in
        let identify = builder as! XIdentify
        identify.setOnce("ID", value:"MRCM")
        identity.set("action", value:"loginSuccess")
        identity.add("visitCount", increment: 1)
    }))
```

### Objective-C

```Objective-C
    [DOX groupIdentify:@"company" value:@"gsshop" identify:[XIdentify builder:^(XIdentify *identify) {
        [identify setOnce:@"ID" value:@"MRCM"];
        [identify setOnce:@"action" value:@"loginSuccess"];
        [identify add:@"visitCount" increment:1];
    }]];
```