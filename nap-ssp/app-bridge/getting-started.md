# App Bridge (WebView) - 시작하기

> WebView 기반 하이브리드 앱에서 JavaScript 한 줄로 NapSSP 광고를 요청하고, 네이티브(Android/iOS)가 광고를 렌더링하는 브릿지 패턴 연동 가이드  
> 문의: [nap_adx@nasmedia.co.kr](mailto:nap_adx@nasmedia.co.kr)

## 이 가이드가 필요한 경우

- 앱 안의 **WebView** 화면에서 광고를 보여주고 싶다
- 광고 요청은 **JavaScript**에서 하고, 실제 광고 SDK 호출은 **Native(Android/iOS)**에서 처리하고 싶다
- Android와 iOS 모두 **동일한 JS 코드**로 광고를 제어하고 싶다

> 순수 Native 앱의 경우 [Android - 시작하기](../android/getting-started) / [iOS - 시작하기](../ios/getting-started)를 참고하세요.

---

## 아키텍처

```
┌─────────────────────────────────────────┐
│              WebView (HTML/JS)           │
│  callNative('loadAd', {format:'banner'}) │
│            │                             │
│            ▼                             │
│  window.NapSspBridge.postMessage(...)    │  ← Android
│  window.webkit.messageHandlers...        │  ← iOS
└──────────────┬──────────────────────────┘
               │  JSON 메시지
               ▼
┌─────────────────────────────────────────┐
│           Native Bridge Layer            │
│  • JSON 파싱 & 포맷 검증                  │
│  • 즉시 ACK 응답 → JS                    │
└──────────────┬──────────────────────────┘
               │
               ▼
┌─────────────────────────────────────────┐
│         NapSSP / AdMixer SDK             │
│  • 광고 로드 / 노출 / 클릭 / 종료 이벤트   │
└──────────────┬──────────────────────────┘
               │  SDK 이벤트 콜백
               ▼
        window.onNapSspMessage(responseJson)
```

> **중요:** `loadAd` 즉시 응답(`Accepted banner`)은 **광고 로드 성공이 아닙니다**. 브릿지가 요청을 수신했다는 ACK입니다. 실제 로드 성공/실패는 `event` action으로 별도 전달됩니다.

---

## 지원 광고 포맷

| format | 광고 종류 | 표시 방식 | 화면 높이 |
|---|---|---|---|
| `banner` | 배너 (320×100) | WebView 하단 Native 영역 | 100dp/pt |
| `native` | 네이티브 | WebView 하단 Native 영역 | 350~400dp/pt |
| `video` | 아웃스트림 비디오 | WebView 하단 Native 영역 | 250dp/pt |
| `rewardVideo` | 보상형 비디오 | 전체화면 (SDK 자동 표시) | — |
| `interstitialVideo` | 전면 비디오 | 전체화면 (SDK 자동 표시) | — |
| `interstitialBanner` | 전면 배너/팝업 | 전체화면 (SDK 자동 표시) | — |

---

## 시작 전 준비물

| 항목 | 설명 |
|---|---|
| `MEDIA_KEY` | Nasmedia에서 발급받은 매체 키 |
| `AD_UNIT_ID` | 광고 단위 ID (포맷마다 별도) |
| Android | JDK 17, Android Studio, minSdk 21 이상 |
| iOS | macOS, Xcode 15.3 이상, iOS 14.0 이상 |

---

## Android 연동

### SDK 설치

**`android/settings.gradle.kts`** — Maven 저장소 추가:

```kotlin
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
        maven(url = "https://devrepo.kakao.com/nexus/content/groups/public/")  // AdFit
        maven(url = "https://artifact.bytedance.com/repository/pangle/")        // Pangle
    }
}
```

**`android/app/build.gradle.kts`** — 의존성 추가:

```kotlin
dependencies {
    implementation("io.github.nasmedia-tech:admixer-ssp:1.0.23")
    implementation("com.google.android.gms:play-services-ads-identifier:18.9.0")

    // 미디에이션 어댑터 (사용하는 네트워크만 선택)
    implementation("io.github.nasmedia-tech:admixer-admanager:1.0.14")  // Google Ad Manager
    implementation("io.github.nasmedia-tech:admixer-adfit:1.0.10")      // Kakao AdFit
    implementation("io.github.nasmedia-tech:admixer-pangle:1.0.10")     // Pangle
    implementation("io.github.nasmedia-tech:admixer-applovin:1.0.8")    // AppLovin
    implementation("io.github.nasmedia-tech:admixer-unity:1.0.6")       // Unity Ads
}
```

**`AndroidManifest.xml`** — 권한:

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="com.google.android.gms.permission.AD_ID" />

<!-- Google Mobile Ads 사용 시 -->
<application>
    <meta-data
        android:name="com.google.android.gms.ads.APPLICATION_ID"
        android:value="YOUR_GOOGLE_MOBILE_ADS_APP_ID" />
</application>
```

**ProGuard / R8** (`proguard-rules.pro`):

```proguard
-keep class com.nasmedia.admixerssp.** { *; }
-keep class com.nasmedia.admanager.** { *; }
-keep class com.nasmedia.adfit.** { *; }
-keep class com.nasmedia.pangle.** { *; }
-keep class com.nasmedia.applovin.** { *; }
-keep class com.nasmedia.unity.** { *; }
-keep class com.kakao.adfit.** { *; }
-keep class com.google.android.gms.ads.** { *; }
```

### 키 설정 (`NapSspConfig.kt`)

```kotlin
package com.yourapp  // 실제 패키지명으로 변경

object NapSspConfig {
    const val MEDIA_KEY = "YOUR_MEDIA_KEY"
    const val PANGLE_APP_ID = "YOUR_PANGLE_APP_ID"

    val AD_UNIT_IDS = mapOf(
        "banner"               to "YOUR_BANNER_AD_UNIT_ID",
        "native"               to "YOUR_NATIVE_AD_UNIT_ID",
        "video"                to "YOUR_VIDEO_AD_UNIT_ID",
        "rewardVideo"          to "YOUR_REWARD_AD_UNIT_ID",
        "interstitialVideo"    to "YOUR_INTERSTITIAL_VIDEO_AD_UNIT_ID",
        "interstitialBanner"   to "YOUR_INTERSTITIAL_BANNER_AD_UNIT_ID",
    )
}
```

> 키를 소스 코드에 직접 커밋하지 말고 `BuildConfig`나 서버 설정으로 주입하세요.

### 브릿지 + WebView 화면

Android는 `JavascriptInterface`로 브릿지를 구현합니다. JS → Native 메시지 수신, SDK 이벤트 → JS 콜백 전달의 양방향 통신 구조입니다.

핵심 흐름:

```
JS: window.NapSspBridge.postMessage(jsonString)
        ↓
@JavascriptInterface fun postMessage(jsonString: String)
        ↓
NapSspSdkIntegration.bannerView(context) 등 호출
        ↓
onAdEventCallback → bridge.sendResponse("event", "success", "...")
        ↓
JS: window.onNapSspMessage(responseStr)
```

전체 구현체는 [app-bridge-nap-ssp-docs](https://github.com/Nasmedia-Tech/app-bridge-nap-ssp-docs) 레포의 `integration-package/android/` 참조.

---

## iOS 연동

### SDK 설치

**SPM 방식** — Xcode → File → Add Package Dependencies:

| 패키지 | Repository |
|---|---|
| nap ssp Core (필수) | `https://github.com/Nasmedia-Tech/iOS-SSP-SPM.git` |
| nap ssp Mediation (필수) | `https://github.com/Nasmedia-Tech/iOS-SSP-Mediation-SPM.git` |
| Google AdManager | `https://github.com/Nasmedia-Tech/iOS-SSP-GAM-SPM.git` |
| Kakao AdFit | `https://github.com/Nasmedia-Tech/iOS-SSP-AdFit-SPM.git` |
| Pangle | `https://github.com/Nasmedia-Tech/iOS-SSP-Pangle-SPM.git` |
| AppLovin | `https://github.com/Nasmedia-Tech/iOS-SSP-AppLovin-SPM.git` |
| Unity Ads | `https://github.com/Nasmedia-Tech/iOS-SSP-UnityAds-SPM.git` |

**CocoaPods 방식** (`Podfile`):

```ruby
platform :ios, '14.0'
target 'YourApp' do
  use_frameworks!
  pod 'AdMixerMediation'
  pod 'AdMixerMediationGAM'      # Google Ad Manager
  pod 'AdMixerMediationAdFit'    # Kakao AdFit
  pod 'AdMixerMediationPangle'   # Pangle
  pod 'AdMixerMediationAppLovin' # AppLovin
  pod 'AdMixerMediationUnityAds' # Unity Ads
end
```

### Info.plist 설정

```xml
<!-- ATT 동의 문구 (IDFA 수집 시 필수) -->
<key>NSUserTrackingUsageDescription</key>
<string>맞춤형 광고 제공을 위해 기기 식별자를 사용합니다.</string>

<!-- ATS -->
<key>NSAppTransportSecurity</key>
<dict><key>NSAllowsArbitraryLoads</key><true/></dict>

<!-- Google Mobile Ads 사용 시 -->
<key>GADApplicationIdentifier</key>
<string>YOUR_GOOGLE_MOBILE_ADS_APP_ID</string>
```

### 키 설정 (`NapSspConfig.swift`)

```swift
enum NapSspConfig {
    static let mediaKey: Int = YOUR_MEDIA_KEY

    static let adUnitIDs: [String: Int] = [
        "banner":             YOUR_BANNER_AD_UNIT_ID,
        "native":             YOUR_NATIVE_AD_UNIT_ID,
        "video":              YOUR_VIDEO_AD_UNIT_ID,
        "rewardVideo":        YOUR_REWARD_AD_UNIT_ID,
        "interstitialVideo":  YOUR_INTERSTITIAL_VIDEO_AD_UNIT_ID,
        "interstitialBanner": YOUR_INTERSTITIAL_BANNER_AD_UNIT_ID,
    ]
}
```

### 브릿지 + WebView 화면

iOS는 `WKScriptMessageHandler`로 브릿지를 구현합니다.

핵심 흐름:

```
JS: window.webkit.messageHandlers.NapSspBridge.postMessage(jsonString)
        ↓
func userContentController(_ ucc: WKUserContentController, didReceive message: WKScriptMessage)
        ↓
NapSspSdkIntegration.banner(rootVC:) 등 호출
        ↓
onAdEventCallback → sendResponse("event", "success", "...")
        ↓
JS: window.onNapSspMessage(responseStr)
```

전체 구현체는 [app-bridge-nap-ssp-docs](https://github.com/Nasmedia-Tech/app-bridge-nap-ssp-docs) 레포의 `integration-package/ios/` 참조.

---

## JavaScript 연동 (공통)

Android / iOS 모두 동일한 JS 코드를 사용합니다.

### JS → Native 요청

```javascript
function callNative(action, params = {}) {
    const request = JSON.stringify({ action, params });

    if (window.NapSspBridge?.postMessage) {
        window.NapSspBridge.postMessage(request);          // Android
    } else if (window.webkit?.messageHandlers?.NapSspBridge) {
        window.webkit.messageHandlers.NapSspBridge.postMessage(request);  // iOS
    } else {
        console.warn('NapSspBridge를 찾을 수 없습니다.');
    }
}
```

| action | params | 설명 |
|---|---|---|
| `init` | — | SDK 초기화 |
| `loadAd` | `format`, `adUnitId?` | 광고 로드 요청 |
| `clearAds` | — | 표시 중인 광고 해제 |

### Native → JS 응답

```javascript
window.onNapSspMessage = function(responseStr) {
    const res = JSON.parse(responseStr);
    // res.action : "init" | "loadAd" | "clearAds" | "event" | "error"
    // res.status : "success" | "error"
    // res.data   : 메시지 문자열

    if (res.action === 'event') {
        handleAdEvent(res.data);
    }
};
```

### 이벤트 종류

| 이벤트 | 설명 |
|---|---|
| `loaded` | 광고 로드 성공 |
| `displayed` | 광고 노출 |
| `clicked` | 광고 클릭 |
| `rewarded` | 보상 지급 (rewardVideo 전용) |
| `completed` | 광고 재생 완료 |
| `skipped` | 광고 건너뜀 |
| `closed` | 전면/보상 광고 닫힘 |
| `failed` | 광고 로드/표시 실패 |

### 이벤트 처리 예시

```javascript
window.onNapSspMessage = function(responseStr) {
    const res = JSON.parse(responseStr);

    if (res.action === 'event' && res.status === 'success') {
        // res.data 형식: "[format] event: detail"
        // 예: "[banner] loaded: 104704"
        // 예: "[rewardVideo] rewarded: 103722"
        // 예: "[banner] failed: [-1] No fill"

        const [formatPart, eventPart] = res.data.split('] ');
        const format = formatPart.replace('[', '');
        const [event] = eventPart.split(': ');

        switch (event) {
            case 'loaded':
                console.log(`${format} 광고 로드 성공`);
                break;
            case 'rewarded':
                grantReward();  // 리워드 지급 처리
                break;
            case 'closed':
                break;
            case 'failed':
                console.warn(`${format} 광고 로드 실패:`, res.data);
                break;
        }
    }
};
```

### 완성된 HTML 샘플

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <style>
        body { font-family: -apple-system, sans-serif; padding: 20px; }
        button {
            display: block; width: 100%; padding: 14px; margin: 8px 0;
            background: #E42429; color: white; border: none; border-radius: 8px;
            font-size: 16px; cursor: pointer;
        }
        .log { background: #1e1e1e; color: #4ec94e; padding: 12px; border-radius: 8px;
               font-family: monospace; font-size: 12px; min-height: 120px; margin-top: 16px; }
    </style>
</head>
<body>
    <h2>NapSSP Bridge</h2>
    <button onclick="callNative('init')">1. SDK 초기화</button>
    <button onclick="callNative('loadAd', {format: 'banner'})">배너 광고</button>
    <button onclick="callNative('loadAd', {format: 'rewardVideo'})">보상형 광고</button>
    <button onclick="callNative('loadAd', {format: 'interstitialBanner'})">전면 배너</button>
    <button onclick="callNative('clearAds')" style="background:#666">광고 해제</button>
    <div class="log" id="log">브릿지 연결 대기 중...</div>

    <script>
        function callNative(action, params = {}) {
            const request = JSON.stringify({ action, params });
            log(`→ ${action} ${params.format || ''}`);
            if (window.NapSspBridge?.postMessage) {
                window.NapSspBridge.postMessage(request);
            } else if (window.webkit?.messageHandlers?.NapSspBridge) {
                window.webkit.messageHandlers.NapSspBridge.postMessage(request);
            } else {
                log('<span style="color:red">브릿지를 찾을 수 없습니다</span>');
            }
        }

        window.onNapSspMessage = function(responseStr) {
            const res = JSON.parse(responseStr);
            const color = res.status === 'success' ? '#4ec94e' : '#f44336';
            log(`<span style="color:${color}">← ${res.action} [${res.status}] ${res.data}</span>`);
        };

        function log(msg) {
            const el = document.getElementById('log');
            el.innerHTML += `<div>[${new Date().toLocaleTimeString()}] ${msg}</div>`;
            el.scrollTop = el.scrollHeight;
        }
    </script>
</body>
</html>
```

---

## 주의사항

- `loadAd` 즉시 응답은 **ACK**(요청 수신 확인)이며, 광고 로드 성공을 의미하지 않습니다.
- 클라이언트(JS) 600ms, 네이티브 500ms **디바운스**가 적용되어 연속 요청이 차단됩니다.
- `adUnitId`는 Android/iOS 모두 `Int` 타입이므로 JS에서 숫자 문자열로 전달하세요.

---

## 자주 묻는 질문

**브릿지를 찾을 수 없다는 오류가 납니다**

| 원인 | 해결 |
|---|---|
| 일반 브라우저에서 테스트 중 | 반드시 앱 내 WebView에서 확인하세요 |
| Android: `addJavascriptInterface` 미호출 | WebView 설정에서 `addJavascriptInterface(bridge, "NapSspBridge")` 확인 |
| iOS: `userContentController.add` 미호출 | `config.userContentController.add(bridge, name: "NapSspBridge")` 확인 |
| 페이지 로드 완료 전 JS 호출 | `onPageFinished` 또는 `navigationDelegate` 완료 후 호출 |

**`loadAd` 응답은 success인데 광고가 보이지 않습니다**

`loadAd` 즉시 응답은 브릿지 수신 확인(ACK)입니다. `window.onNapSspMessage`에서 `action === "event"`이고 data에 `loaded` 또는 `failed`가 들어오는지 확인하세요.

```javascript
// ACK (브릿지가 요청을 받았다는 의미)
{ "action": "loadAd", "status": "success", "data": "Accepted banner" }

// 실제 광고 로드 성공
{ "action": "event", "status": "success", "data": "[banner] loaded: 104704" }

// 실제 광고 로드 실패
{ "action": "event", "status": "success", "data": "[banner] failed: [-1] No fill" }
```

---

## 운영 전 체크리스트

**키 설정**
- [ ] `MEDIA_KEY`를 실제 운영 값으로 교체
- [ ] `AD_UNIT_ID`를 포맷별 실제 운영 값으로 교체
- [ ] 키를 소스 코드에 직접 커밋하지 않음 (`BuildConfig`, `xcconfig`, 서버 설정 활용)

**Android**
- [ ] ProGuard 규칙 추가
- [ ] SDK 로그 레벨을 운영 환경에 맞게 조정

**iOS**
- [ ] `NSUserTrackingUsageDescription` 문구 작성
- [ ] ATT 요청 타이밍 구현

**브릿지**
- [ ] JS `callNative` 호출 전 `init` 먼저 호출
- [ ] `clearAds` 호출로 이전 광고 정리
- [ ] `window.onNapSspMessage`에서 `failed` 이벤트 처리
- [ ] Android / iOS 양쪽에서 동일한 `format` 값 사용
