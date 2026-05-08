# iOS Tracking SDK - 시작하기

> 문의: [nap_adx@nasmedia.co.kr](mailto:nap_adx@nasmedia.co.kr)

## 요구사항

- **최소 OS**: iOS 13.0 이상
- **최소 Xcode**: 15.0 이상
- **언어**: Swift

---

## Step 1. SDK 설치

### SPM (Swift Package Manager)

**Project > Package Dependencies**에서 추가:

```
https://github.com/Nasmedia-Tech/iOS-Tracking-SPM.git
```

### CocoaPods

```ruby
pod 'NapTracking'
```

---

## Step 2. SDK 초기화

`AppDelegate.application(_:didFinishLaunchingWithOptions:)`에서 1회 초기화합니다.

```swift
import GwangyTracking

@main
class AppDelegate: UIResponder, UIApplicationDelegate {
    func application(_ application: UIApplication,
                     didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {

        let config = TrackingConfig(
            apiKey: "발급받은 API Key",
            endpoint: "https://tracking.nasmedia.co.kr/collect",
            flushInterval: 30_000,        // 이벤트 전송 주기 (ms)
            debug: false,
            logLevel: .info,
            maxBatchSize: 50,
            enableCrashTracking: true     // 크래시 자동 수집
        )

        GYTracking.initialize(application: application, config: config)

        return true
    }
}
```

---

## Step 3. 이벤트 추적

### 커스텀 이벤트

```swift
// 이벤트 이름만
GYTracking.track(name: "button_click")

// 이벤트 + 속성
GYTracking.track(name: "purchase", props: [
    "item_id": "product_123",
    "price": 9900,
    "currency": "KRW"
])
```

### 화면 전환

```swift
GYTracking.screen(name: "MainScreen")

GYTracking.screen(name: "ProductDetailScreen", properties: [
    "product_id": "product_123"
])
```

### 사용자 속성

```swift
GYTracking.identify(attrs: [
    "user_id": "user_abc",
    "plan": "premium",
    "age": 28
])
```

---

## Step 4. 자동 추적

SDK 초기화 시 `enableCrashTracking: true` 설정 시 크래시가 자동 수집됩니다.  
화면 전환(`UIViewController` 수명주기)도 자동 추적됩니다.

---

## 설정 옵션

| 옵션 | 타입 | 기본값 | 설명 |
|------|------|--------|------|
| `apiKey` | String | 필수 | 발급받은 API Key |
| `endpoint` | String | 필수 | 이벤트 수집 서버 URL |
| `flushInterval` | Int | 30000 | 이벤트 전송 주기 (ms) |
| `maxBatchSize` | Int | 50 | 배치 최대 이벤트 수 |
| `enableCrashTracking` | Bool | false | 크래시 자동 수집 여부 |
| `logLevel` | LogLevel | .info | 로그 레벨 |
| `debug` | Bool | false | 디버그 모드 |
