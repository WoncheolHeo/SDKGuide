* [DOX](./README.md#DOX)
	* [Installation](./README.md#DOX_INSTALL)
		* [SDK Download](./README.md#DOX_SDK_Download)
		* [Key 등록](./README.md#DOX_KEY)
	* [기본 분석 적용](./README.md#DOX_BASE)
		* [SDK init](./README.md#DOX_INIT)
	* [Hybrid 앱 분석 방법](./README.md#DOX_HYBRID)
	* [유입 경로 분석](./README.md#DOX_ROUTE)
		* [외부 유입 경로 분석](./README.md#--외부-유입-경로-분석)
		* [푸시 분석](./README.md#--푸시-분석)
	* [이벤트 분석](./README.md#이벤트-분석)
		* [회원 분석](./README.md#DOX_USER)
		* [GroupIdentify 분석](./README.md#GroupIdentify-분석)
		* [UserIdentify 분석](./README.md#UserIdentify-분석)
		* [Log Event 분석](./README.md#Log-Event-분석)
		* [Log Conversion 분석](./README.md#Log-Conversion-분석)
		* [Log Revenue 분석](./README.md#Log-Revenue-분석)

# DOX

### <a id="DOX_INSTALL"></a> Installation

##### <a id="DOX_SDK_Download"></a> - SDK Download
Android 프로젝트 app/build.gradle 파일 dependencies 항목에 아래와 같이 추가

```gradle
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar']) 
    ....
    implementation 'com.sdk.wisetracker.base:base_module_test:0.0.3' // BASE
    implementation 'com.sdk.wisetracker.dox:dox_module_test:0.0.3'   // DOX
}
```

##### <a id="DOX_KEY"></a> - Key 등록
Android 프로젝트의 app/res/values/strings.xml 파일에 제공받은 App Analytics Key 정보를 추가
```xml
<string-array name="dotAuthorizationKey">
    <item name="usdMode">3</item>
    <item name="domain">http://collector.naver.wisetracker.co.kr</item> // DOT END POINT
    <item name="domain_x">http://collector.naver.wisetracker.co.kr</item> // DOX END POINT
    <item name="serviceNumber">103</item>
    <item name="expireDate">14</item>
    <item name="isDebug">false</item>
    <item name="isInstallRetention">true</item>
    <item name="isFingerPrint">true</item>
    <item name="accessToken">access_token_string</item>
</string-array>
```

### <a id="DOX_BASE"></a> 기본 분석 적용

#### <a id="DOX_INIT"></a> - SDK init
Android 프로젝트 MainActivity의 onCreate(Bundle savedInstanceState) 메소드에서 SDK Initialization 메소드 추가

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    DOX.initialization(this); // 추가
}
```

### <a id="DOX_HYBRID"></a> Hybrid 앱 분석 방법
Hybrid 앱의 경우 앱 내에서 WebView 를 사용하여 웹 컨텐츠를 서비스 하기도 합니다
이와 같이 Webview 에 의해서 보여지는 웹 컨텐츠의 경우에는 위에서 설명된 Native 화면과는 다른 방식으로 동작하기 때문에, 별도의 분석 코드 적용이 필요합니다
분석 대상 앱이 만약 Hybrid 앱인 경우에는 아래의 코드를 참고하여 웹 컨텐츠도 분석할 수 있도록 적용을 해야합니다

```java
@Override
protected void onCreate(@Nullable Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    WebView webView = findViewById(R.id.web_view);
    DOX.setWebView(webView); // 추가
}
```
***WebView를 사용하는 Activity 에서 적용할 분석코드***

### <a id="DOX_ROUTE"></a> 유입 경로 분석

#### - 외부 유입 경로 분석
앱이 설치된 이후 DeepLink를 통해서 앱이 실행되는 경로 분석이 필요한 경우 아래와 같이 setDeepLink() 함수를 사용하면 분석이 가능합니다. 정확한 앱 실행 경로 분석 결과를 위해서 이 함수의 적용은 딥링크가 들어오는 모든 경로에 적용되는 것을 권장합니다.

```java
DOX.setDeepLink(context, intent);
```

#### - 푸시 분석

- FCM push token 분석 방법
push token은 FCM 푸시 발송 시스템에서 메시지 전송시 수신 대상이 되는 개개인 별로 Unique하게 발급되는 일종의 식별ID 값입니다. 
push token을 분석하기 위해서 MainActivity 의 onCreate() 함수에 아래와 같이 FirebaseInstanceId 를 사용하여 분석 코드를 적용합니다.

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    FirebaseInstanceId
    .getInstance()
    .getInstanceId()
    .addOnSuccessListener(/*context*/ this, new OnSuccessListener() {
        @Override
        public void onSuccess(InstanceIdResult instanceIdResult) {
            String newToken = instanceIdResult.getToken();
            DOX.setPushToken(this, newToken); // 추가
        }
    });
} 
```

### 이벤트 분석

#### <a id="DOX_USER"></a> 회원 분석
```java
DOX.setUserId("USER ID");
```
#### GroupIdentify 분석

```java
DOX.groupIdentify("USER ID", "value", new XIdentify.Builder()
                .add("add", 1)
                .append("append", "append")
                .build());
```

**\<XIdentify>**

| Class 이름 |  Method 이름 | 파라미터 | 
| :-----: |  :-----: | :-----: | 
| XIdentify | set(key, value) | set 값을 전달 | 
| XIdentify | setOnce(key, value) | setonce 값을 전달 | 
| XIdentify | unset(value) | unset 값을 전달 | 
| XIdentify | add(key, increment) | add 값을 전달 | 
| XIdentify | append(key, value) | append 값을 전달 | 
| XIdentify | prepend(key, value) | prepend 값을 전달 |


#### UserIdentify 분석

```java
DOX.userIdentify(new XIdentify.Builder()
                .set("set", "\"\n1\"")
                .set("set2", "2")
                .set("add2", new XProperties.Builder()
                        .set("key1", "key1")
                        .set("key2", new XProperties.Builder()
                                .set("key2", "key2")
                                .build())
                        .build())
                .unset("unset")
                .unset("unset2")
                .add("add1", 1)
                .add("add2", 12)
                .add("add3", 123)
                .add("add4", 1234)
                .build());
```

**\<XIdentify>**

| Class 이름 |  Method 이름 | 파라미터 | 
| :-----: |  :-----: | :-----: | 
| XIdentify | set(key, value) | set 값을 전달 | 
| XIdentify | setOnce(key, value) | setonce 값을 전달 | 
| XIdentify | unset(value) | unset 값을 전달 | 
| XIdentify | add(key, increment) | add 값을 전달 | 
| XIdentify | append(key, value) | append 값을 전달 | 
| XIdentify | prepend(key, value) | prepend 값을 전달 |

#### Log Event 분석

```java
DOX.logEvent(new XEvent.Builder()
                        .setEventName("log event")
                        .setProperties(new XProperties.Builder()
                                .set("put1", "put value")
                                .build())
                        .build()));
```

**\<XEvent>**

| Class 이름 |  Method 이름 | 파라미터 | 
| :-----: |  :-----: | :-----: | 
| XEvent | setEventName(value) | event name 값을 전달 | 
| XEvent | setProperties(value) | xProperties 값을 전달 | 
| XProperties | set(key, value) | xProperties key, value 값을 전달 | 

#### Log Conversion 분석

```java
DOX.logConversion(
                new XConversion.Builder()
                        .setEventName("Conversion")
                        .setProperties(
                                new XProperties.Builder()
                                        .set("item1", "value")
                                        .set("item2", new XProperties.Builder()
                                                .set("2 depth", 2)
                                                .set("item3", new XProperties.Builder()
                                                        .set("3 depth", 3)
                                                        .build()
                                                ))
                                        .build())
                        .build()
        );
```

**\<XConversion>**

| Class 이름 |  Method 이름 | 파라미터 | 
| :-----: |  :-----: | :-----: | 
| XConversion | setEventName(value) | event name 값을 전달 | 
| XConversion | setProperties(value) | xProperties 값을 전달 | 
| XProperties | set(key, value) | xProperties key, value 값을 전달 | 

#### Log Revenue 분석
```java
DOX.logRevenue(new XRevenue.Builder()
                .setRevenueType("revenue")
                .setCurrency("krw")
                .setOrderNo("ordno")
                .setProduct(new XProduct.Builder()
                        .setProductCode("prc code")
                        .setProductOrderNo("prc ord no")
                        .setDetailCategory("detail")
                        .setProperties(new XProperties.Builder()
                                .set("pr1", "pr1")
                                .set("pr2", new XProperties.Builder()
                                        .set("pr2 deptch", "1")
                                        .build())
                                .build())
                        .build())
                .setProperties(new XProperties.Builder()
                        .set("revenue", "1")
                        .set("revenue2", new XProperties.Builder()
                                .set("key1", "key1")
                                .build())
                        .build())
                .build()
        );
```

**\<XRevenue>**

| Class 이름 |  Method 이름 | 파라미터 | 
| :-----: |  :-----: | :-----: | 
| XRevenue | setOrderNo(value) | 주문번호 값을 전달 | 
| XRevenue | setRevenueType(value) | type 값을 전달 | 
| XRevenue | setCurrency(value) | currency 값을 전달 | 
| XRevenue | setProduct(product) | product 값을 전달 | 
| XRevenue | setProductList(List<Product> value) | product list 값을 전달 | 
| XProduct | firstCategory(value) | firstCategory 값을 전달 | 
| XProduct | secondCategory(value) | secondCategory 값을 전달 | 
| XProduct | thirdCategory(value) | thirdCategory 값을 전달 | 
| XProduct | detailCategory(value) | detailCategory 값을 전달 | 
| XProduct | productCode(value) | productCode 값을 전달 | 
| XProduct | orderAmount(value) | orderAmount 값을 전달 | 
| XProduct | orderQuantity(value) | orderQuantity 값을 전달 | 
| XProduct | productOrderNo(value) | product order no 값을 전달 | 
| XProduct | setProperties(value) | xProperties 값을 전달 | 
| XRevenue | setProperties(value) | xProperties 값을 전달 | 	
| XProperties | set(key, value) | xProperties key, value 값을 전달 | 
