# iOS NapTrackerSDK 탑재 가이드

> 문의: [nap_adx@nasmedia.co.kr](mailto:nap_adx@nasmedia.co.kr)

## 개요

- **최소 지원 OS**: iOS 13.0 이상
- **최소 Xcode**: Xcode 16 이상
- **설치 방식**: CocoaPods, SPM 지원
- 샘플 앱: [iOS-NapTrackerSDK-SampleApp](https://github.com/Nasmedia-Tech/iOS-NapTrackerSDK-SampleApp)

---

## 1. SDK 설치

### 1-1. CocoaPods로 설치

CocoaPods가 없다면 설치 후 초기화합니다.

```bash
pod init
```

`Podfile`에 NapTrackerSDK를 추가합니다.

```ruby
target 'MyApp' do
  use_frameworks!
  pod 'NapTrackerSDK'
end
```

Pod을 설치합니다.

```bash
pod install
```

> **Target > Build Settings > User Script Sandboxing → NO** 로 변경해주세요.

### 1-2. SPM으로 설치

**Project > Package Dependencies** 탭에서 아래 주소를 추가합니다.

```
https://github.com/Nasmedia-Tech/iOS-NapTrackerSDK-SPM
```

---

## 2. SDK 설정

### 2-1. Info.plist - 추적 동의 문구 입력

`Info.plist`의 **Privacy - Tracking Usage Description** 항목에 사용자에게 보여줄 추적 권한 요청 문구를 입력합니다.

### 2-2. ATT 팝업 실행 (필수)

ATT(App Tracking Transparency) 프레임워크로 추적 권한 팝업을 실행합니다.

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {

    func application(_ application: UIApplication,
                     didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        requestATT()
        return true
    }

    func requestATT() {
        if #available(iOS 14, *) {
            ATTrackingManager.requestTrackingAuthorization { status in
                print("ATT status: \(status.rawValue)")
            }
        }
    }
}
```

---

## 3. SDK 초기화

> 로그를 확인하려면 초기화 **전에** 로그 레벨을 설정하세요. (기본값: none)

**[Swift]**

```swift
NapTracker.shared.setLog(level: .debug)  // 선택
NapTracker.shared.initialize()           // 필수
```

**[Objective-C]**

```objective-c
[[NapTracker shared] setLogWithLevel:LogLevelDebug];
[[NapTracker shared] initialize];
```

### AppDelegate 전체 예시

```swift
import NapTrackerSDK

class AppDelegate: UIResponder, UIApplicationDelegate {

    func application(_ application: UIApplication,
                     didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {

        requestATT()
        NapTracker.shared.setLog(level: .debug)
        NapTracker.shared.initialize()

        return true
    }

    func requestATT() {
        if #available(iOS 14, *) {
            ATTrackingManager.requestTrackingAuthorization { status in
                print("ATT status: \(status.rawValue)")
            }
        }
    }
}
```

---

## 4. 주요 기능

### 4-1. 사용자 속성 설정 (선택)

**사용자 ID 설정:**

```swift
NapTracker.shared.setUserId(userId: "SampleUserId")
```

```objective-c
[[NapTracker shared] setUserIdWithUserId:@"SampleUserId"];
```

**사용자 맞춤 속성 설정:**

```swift
NapTracker.shared.setUserProperty("user_tier", "gold")
```

```objective-c
[[NapTracker shared] setUserPropertyWithKey:@"user_tier" value:@"gold"];
```

### 4-2. 이벤트 기록

**[Swift]**

```swift
// 파라미터 없는 이벤트
NapTracker.shared.logEvent(name: "add_to_cart")

// 파라미터 있는 이벤트
let params = ["item_id": "SKU_123", "item_name": "멋진 티셔츠"]
NapTracker.shared.logEvent(name: "add_to_cart", params: params)
```

**[Objective-C]**

```objective-c
// 파라미터 없는 이벤트
[[NapTracker shared] logEvent:@"add_to_cart"];

// 파라미터 있는 이벤트
[[NapTracker shared] logEvent:@"add_to_cart"
                      params:@{@"item_id": @"SKU_123", @"item_name": @"멋진 티셔츠"}];
```

### 4-3. 화면 전환 이벤트 수집 (선택)

**UIKit 기반 앱 — 자동 수집:**

SDK 초기화 후 아래를 호출하면 `UIViewController` 전환이 자동으로 수집됩니다.

```swift
NapTracker.shared.setAutoScreenTrackingEnabled(true)
```

```objective-c
[[NapTracker shared] setAutoScreenTrackingEnabled:YES];
```

**SwiftUI 기반 앱 — 수동 수집:**

```swift
NapTracker.shared.logScreenView(screenName: "YourScreenName")
```

```objective-c
[[NapTracker shared] logScreenViewWithScreenName:@"viewController"];
```
