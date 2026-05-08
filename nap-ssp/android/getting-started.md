# Android SDK - 시작하기

> 문의: [nap_adx@nasmedia.co.kr](mailto:nap_adx@nasmedia.co.kr)

## 버전 기록

| 날짜 | 버전 | 변경사항 |
|------|------|---------|
| 2026-02-20 | 1.0.20 | 미디에이션 업데이트 / 네트워크 버전 업데이트 (Adfit, Pangle, Unity Ads) / 리워드 콜백 추가 |
| 2026-01-21 | 1.0.19 | 네트워크 SDK 리워드 커스텀 파람 추가 / 리워드 내부 로깅 URL 추가 |
| 2026-01-07 | 1.0.18 | 네트워크 버전 업데이트 / 리워드 이벤트 추가 (`EARNEDREWARD`) |
| 2025-10-30 | 1.0.16 | 소재 사이즈 수정 기능 추가 |
| 2025-10-16 | 1.0.15 | Unity Ads 추가 |
| 2025-10-01 | 1.0.14 | 미디에이션 처리 로직 수정 |
| 2025-08-28 | 1.0.13 | 전면배너 옵션 추가 |
| 2025-08-18 | 1.0.12 | Applovin 추가 |
| 2025-07-24 | 1.0.10 | Mobwith, Pangle 추가 |
| 2025-04-14 | 1.0.8 | Kakao Adfit 추가 |
| 2024-10-07 | 1.0.0 | 최초 릴리즈 |

## 요구사항

- **최소 OS**: Android 5.0 (API 레벨 21) 이상
- Android Studio 최신 버전 권장

## 샘플 프로젝트

- [Android Java SDK Sample](https://github.com/Nasmedia-Tech/AOS-AdMixerSSP-TestApp/tree/main/AdMixerSDKSample)
- [Android Kotlin SDK Sample](https://github.com/Nasmedia-Tech/AOS-AdMixerSSP-TestApp/tree/main/AdMixerSDKKotlinSample)

---

## Step 1. Gradle 설정

### 프로젝트 최상위 `build.gradle`

```gradle
allprojects {
    repositories {
        google()
        mavenCentral()
    }
}
```

### 앱 모듈 `build.gradle` — 의존성 추가

**라이브러리 버전은 항상 최신 버전으로 유지하세요.**

```gradle
dependencies {
    // (필수) nap ssp SDK
    implementation 'io.github.nasmedia-tech:admixer-ssp:1.0.21'
    // (필수) Google Advertising ID
    implementation 'com.google.android.gms:play-services-ads-identifier:18.9.0'

    // (선택) 미디에이션 네트워크
    implementation 'io.github.nasmedia-tech:admixer-admanager:1.0.14'  // Google AdManager
    implementation 'io.github.nasmedia-tech:admixer-adfit:1.0.10'      // Kakao Adfit
    implementation 'io.github.nasmedia-tech:admixer-pangle:1.0.10'     // Pangle
    implementation 'io.github.nasmedia-tech:admixer-applovin:1.0.8'    // AppLovin
    implementation 'io.github.nasmedia-tech:admixer-unity:1.0.6'       // Unity Ads
}
```

### Adfit / Pangle 추가 Maven 설정

`settings.gradle`의 `dependencyResolutionManagement`에 추가:

```gradle
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
        maven { url 'https://devrepo.kakao.com/nexus/content/groups/public/' }  // Adfit
        maven { url "https://artifact.bytedance.com/repository/pangle/" }        // Pangle
    }
}
```

---

### Google 네트워크 입찰 광고 소스

Google 네트워크 사용 시 아래 광고 소스를 모두 추가해야 합니다 ([Google 개발자 문서](https://developers.google.com/ad-manager/mobile-ads-sdk/android/choose-networks?hl=ko) 참조):

- Pangle / AppLovin / DT Exchange / InMobi / Liftoff Monetize / Meta / Moloco / Unity Ads / Mintegral

---

### 네트워크 중복 사용 시 예외 처리

이미 운영 중인 네트워크가 있는 경우:

```gradle
dependencies {
    implementation("io.github.nasmedia-tech:admixer-admanager:1.0.14") {
        exclude group: "com.google.android.gms", module: "play-services-ads"
    }
    implementation("io.github.nasmedia-tech:admixer-adfit:1.0.10") {
        exclude group: "com.kakao.adfit", module: "ads-base"
    }
}
```

---

## Step 2. SDK 초기화

`Application.onCreate()`에서 **1회** 호출합니다.

```java
public class MyApplication extends android.app.Application {
    public static String MEDIA_KEY = "파트너 사이트에서 발급받은 미디어 키";
    public static String ADUNIT_ID_BANNER = "배너 ADUNIT_ID";
    public static String ADUNIT_ID_INTERSTITIAL_BANNER = "전면배너 ADUNIT_ID";
    public static String ADUNIT_ID_NATIVE = "네이티브 ADUNIT_ID";
    public static String ADUNIT_ID_REWARD_VIDEO = "리워드 동영상 ADUNIT_ID";
    public static String ADUNIT_ID_VIDEO = "인라인 동영상 ADUNIT_ID";
    public static String ADUNIT_ID_INTERSTITIAL_VIDEO = "전면 동영상 ADUNIT_ID";

    @Override
    public void onCreate() {
        super.onCreate();

        // 로그 설정 (개발 시)
        AdMixerLog.setLogLevel(AdMixerLog.LogLevel.VERBOSE);

        // SDK 초기화
        ArrayList<String> adUnits = new ArrayList<>(Arrays.asList(
            ADUNIT_ID_BANNER, ADUNIT_ID_INTERSTITIAL_BANNER, ADUNIT_ID_NATIVE,
            ADUNIT_ID_REWARD_VIDEO, ADUNIT_ID_VIDEO, ADUNIT_ID_INTERSTITIAL_VIDEO
        ));
        AdMixer.getInstance().initialize(this, MEDIA_KEY, adUnits);

        // 미디에이션 어댑터 등록 (사용하는 네트워크만)
        AdMixer.registerAdapter(AdMixer.ADAPTER_ADMANAGER);
        AdMixer.registerAdapter(AdMixer.ADAPTER_ADFIT);
        AdMixer.registerAdapter(AdMixer.ADAPTER_PANGLE);
        AdMixer.registerAdapter(AdMixer.ADAPTER_APPLOVIN);
        AdMixer.registerAdapter(AdMixer.ADAPTER_UNITY);

        // Pangle 초기화 (Pangle 사용 시 필수)
        PAGConfig config = new PAGConfig.Builder()
            .appId("운영팀에서 발급받은 Pangle App ID")
            .build();
        PAGSdk.init(this, config, new PAGSdk.PAGInitCallback() {
            @Override public void success() {}
            @Override public void fail(int code, String msg) {}
        });
    }
}
```

### Google App ID 설정

`AndroidManifest.xml`에 추가:

```xml
<meta-data
    android:name="com.google.android.gms.ads.APPLICATION_ID"
    android:value="운영팀에서 발급받은 Google App ID"/>
```

---

## Step 3. ProGuard 설정

```proguard
-keep class com.nasmedia.admixerssp.** { *; }
-keep class com.nasmedia.admanager.** { *; }
-keep class com.nasmedia.adfit.** { *; }
-keep class com.nasmedia.pangle.** { *; }
-keep class com.nasmedia.applovin.** { *; }
-keep class com.nasmedia.unity.** { *; }
```

---

## FAQ

**Q. 로그 설정 방법은?**  
`AdMixerLog.setLogLevel(AdMixerLog.LogLevel.VERBOSE)` — LogCat에서 상세 로그를 확인할 수 있습니다.

**Q. 하나의 앱에 여러 Media Key를 사용할 수 있나요?**  
한 앱에는 한 개의 Media Key만 사용할 수 있습니다.

**Q. 광고가 노출되지 않을 때?**  
1. LogCat 로그 확인 2. 파트너 사이트 키 설정 확인 3. SDK 초기화 호출 여부 확인 4. Media Key / AdUnit ID 일치 여부 확인
