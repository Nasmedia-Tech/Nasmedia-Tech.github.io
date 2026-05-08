# iOS SDK - 시작하기

> 문의: [nap_adx@nasmedia.co.kr](mailto:nap_adx@nasmedia.co.kr)

## 버전 기록

| 날짜 | 버전 | 변경사항 |
|------|------|---------|
| 2026-03-23 | 2.2.1 | native 지면 loadAD() 시 removeView 추가 |
| 2026-03-17 | 2.2.0 | 버그 픽스 |
| 2026-02-20 | 2.1.8 | 미디에이션 업데이트 |
| 2026-01-20 | 2.1.7 | 배너 320x100 네트워크 추가 |
| 2026-01-09 | 2.1.4 | 네트워크 버전 업데이트 / 리워드 이벤트 추가 |
| 2025-08-26 | 2.0.7 | Applovin 추가 |
| 2025-07-25 | 2.0.4 | Pangle 추가 |
| 2025-04-14 | 2.0.2 | Adfit 추가 |
| 2025-02-28 | 2.0.0 | 버그 픽스 |
| 2024-10-29 | 1.0.0 | 최초 릴리즈 |

## 요구사항

- **최소 OS**: iOS 13.0 이상
- **최소 Xcode**: 15.3 이상
- 설치 방식: CocoaPods 또는 SPM

## 샘플 프로젝트

- [iOS SDK Sample](https://github.com/Nasmedia-Tech/iOS-AdMixerSSP-TestApp)

---

## Step 1. SDK 설치

### CocoaPods

`Podfile`에 추가:

```ruby
target 'MyApp' do
  use_frameworks!
  pod 'AdMixerMediation'              # (필수) nap ssp Mediation
  pod 'AdMixerMediationGAM'           # Google AdManager
  pod 'AdMixerMediationAdFit'         # Kakao AdFit
  pod 'AdMixerMediationPangle'        # Pangle
  pod 'AdMixerMediationAppLovin'      # AppLovin
  pod 'AdMixerMediationUnityAds'      # UnityAds
end
```

```bash
pod update
```

### SPM (Swift Package Manager)

**Project > Package Dependencies**에서 아래 주소 추가:

| 패키지 | Repository |
|--------|-----------|
| nap ssp (필수) | `https://github.com/Nasmedia-Tech/iOS-SSP-SPM.git` |
| nap ssp Mediation (필수) | `https://github.com/Nasmedia-Tech/iOS-SSP-Mediation-SPM.git` |
| Google AdManager | `https://github.com/Nasmedia-Tech/iOS-SSP-GAM-SPM.git` |
| Kakao AdFit | `https://github.com/Nasmedia-Tech/iOS-SSP-AdFit-SPM.git` |
| Pangle | `https://github.com/Nasmedia-Tech/iOS-SSP-Pangle-SPM.git` |

> Dependency Rule: **Up to Next Major Version** 권장. 앱 타겟(App target)에 추가하세요.

### Google 네트워크 입찰 광고 소스 (CocoaPods)

```ruby
pod 'GoogleMobileAdsMediationPangle'
pod 'GoogleMobileAdsMediationFacebook'
pod 'GoogleMobileAdsMediationAppLovin'
pod 'GoogleMobileAdsMediationUnity'
pod 'GoogleMobileAdsMediationVungle'
pod 'GoogleMobileAdsMediationMintegral'
pod 'GoogleMobileAdsMediationFyber'
pod 'GoogleMobileAdsMediationInMobi'
pod 'GoogleMobileAdsMediationMoloco'
```

---

## Step 2. SDK 설정

### 추적 권한 (ATT)

`Info.plist`에 **Privacy - Tracking Usage Description** 추가 후:

```swift
import AppTrackingTransparency

func applicationDidBecomeActive(_ application: UIApplication) {
    Task {
        _ = await ATTrackingManager.requestTrackingAuthorization()
    }
}
```

### Info.plist 설정

| 네트워크 | 설정 항목 |
|---------|---------|
| Google AdManager | `GADApplicationIdentifier` (형식: `ca-app-pub-################~##########`) |
| Kakao AdFit | [SKAdNetwork 가이드](https://adfit.github.io/adfit-ios-sdk/documentation/adfitsdk/skadnetwork) |
| Pangle | [Pangle 가이드](https://www.pangleglobal.com/kr/integration/integrate-pangle-sdk-for-ios) |
| AppLovin | [AppLovin 가이드](https://developers.axon.ai/en/max/ios/overview/integration/) |

> Google App ID 발급: [nap_adx@nasmedia.co.kr](mailto:nap_adx@nasmedia.co.kr)

---

## Step 3. SDK 초기화

`AppDelegate.application(_:didFinishLaunchingWithOptions:)`에서 1회 호출:

```swift
import AdMixerMediation
import GoogleMobileAds
import PAGAdSDK
import AppLovinSDK
import UnityAds

class AppDelegate: NSObject, UIApplicationDelegate {
    func application(_ application: UIApplication,
                     didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]? = nil) -> Bool {

        // AdMixer 초기화 (필수)
        AMMediation.shared.initialize(
            mediaKey: "파트너 사이트에서 발급받은 미디어 키",
            adunitID: ["배너_ADUNIT_ID", "전면_ADUNIT_ID", "네이티브_ADUNIT_ID"]
        )

        // 디버그 로그 활성화 (개발 시)
        AMMediation.shared.setDebugEnabled(isEnabled: true)

        // Google AdManager 초기화 (해당 네트워크 사용 시)
        MobileAds.shared.start()

        // Pangle 초기화 (해당 네트워크 사용 시)
        let pagConfig = PAGConfig.share()
        pagConfig.appID = "운영팀에서 발급받은 Pangle App ID"
        PAGSdk.start(with: pagConfig) { isSuccess, error in }

        // AppLovin 초기화 (해당 네트워크 사용 시)
        let sdkKey = "운영팀에서 발급받은 AppLovin SDK Key"
        let alConfig = ALSdkInitializationConfiguration(sdkKey: sdkKey)
        ALSdk.shared().initialize(with: alConfig) { _ in }

        // UnityAds 초기화 (해당 네트워크 사용 시)
        UnityAds.initialize("운영팀에서 발급받은 Unity App ID")

        return true
    }
}
```

---

## 에러 코드

| 코드 | 설명 |
|------|------|
| 0 `missingBaseURL` | API 요청에 필요한 base URL 누락 |
| 1 `invalidURLString` | 유효하지 않은 URL |
| 2 `invalidServerResponse` | 서버 응답 오류 (네트워크 상태 확인) |
| 3 `decodeError` | 데이터 처리 오류 |
| 4 `apiResponseFail` | 서버 통신 실패 |
| 5 `vastParsingError` | 동영상 광고 데이터 처리 오류 |
| 6 `emptyAd` | 노출 가능한 광고 없음 (잠시 후 재요청) |

---

## 네트워크 호환 버전

| Adapter | SDK | 지원 버전 |
|---------|-----|---------|
| AdMixerMediationGAM | Google-Mobile-Ads-SDK | 12.7.0 ~ 13.3.0 |
| AdMixerMediationAdFit | AdFitSDK | 3.14.7 ~ 3.18.5 (최소 iOS 14) |
| AdMixerMediationPangle | Ads-Global | 7.4.0.8 ~ 7.8.8.8 |
| AdMixerMediationAppLovin | AppLovinSDK | 13.3.1 ~ 13.5.1 |
| AdMixerMediationUnityAds | UnityAds | 4.15.1 ~ 4.16.5 |

---

## FAQ

**Q. 로그 설정 방법은?**  
`AMMediation.shared.setDebugEnabled(isEnabled: true)` — Xcode Console에서 상세 로그 확인

**Q. 하나의 앱에 여러 Media Key를 사용할 수 있나요?**  
한 앱에는 한 개의 Media Key만 사용할 수 있습니다.

**Q. 광고가 노출되지 않을 때?**  
1. Xcode Console 로그 확인 2. 파트너 사이트 키 설정 확인 3. SDK 초기화 호출 여부 확인
