# Android SDK - Unity 연동 가이드

> nap ssp SDK Android Unity 플러그인 연동 가이드입니다. nap ssp Mediation을 지원합니다.  
> 문의: [nap_adx@nasmedia.co.kr](mailto:nap_adx@nasmedia.co.kr)

## 사전 준비

[nap ssp 파트너 사이트](https://publisher.admixer.co.kr/signin)에서 미디어 등록 및 애드유닛 생성을 완료한 뒤 **Media Key**와 **AdUnit ID**를 발급받으세요.

---

## Step 1. SDK 설치

### AndroidManifest.xml 설정

`Assets/Plugins/Android/AndroidManifest.xml` 파일을 생성합니다.

```xml
<manifest>
  <uses-permission android:name="android.permission.INTERNET" />
  <application>
  </application>
</manifest>
```

Google AdManager 또는 AppLovin 사용 시 `<application>` 안에 추가합니다.

```xml
<meta-data
    android:name="com.google.android.gms.ads.APPLICATION_ID"
    android:value="운영팀에서 발급받은 Google App ID"/>
<meta-data android:name="applovin.sdk.key"
    android:value="운영팀에서 발급받은 AppLovin SDK Key"/>
```

### Gradle 의존성 추가

`Assets/Plugins/Android/mainTemplate.gradle` — 필수 및 미디에이션 의존성:

```gradle
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])

    // (필수) nap ssp SDK
    implementation 'io.github.nasmedia-tech:admixer-ssp:1.0.23'
    implementation 'com.google.android.gms:play-services-ads-identifier:18.9.0'

    // (선택) 미디에이션 어댑터
    implementation 'io.github.nasmedia-tech:admixer-admanager:1.0.14'  // Google AdManager
    implementation 'io.github.nasmedia-tech:admixer-adfit:1.0.10'      // Kakao AdFit
    implementation 'io.github.nasmedia-tech:admixer-pangle:1.0.10'     // Pangle
    implementation 'io.github.nasmedia-tech:admixer-applovin:1.0.8'    // AppLovin
    implementation 'io.github.nasmedia-tech:admixer-unity:1.0.6'       // Unity Ads
}
```

### Maven 저장소 추가

`Assets/Plugins/Android/settingsTemplate.gradle`:

```gradle
pluginManagement {
    repositories {
        **ARTIFACTORYREPOSITORY**
        gradlePluginPortal()
        google()
        mavenCentral()
        maven { url 'https://devrepo.kakao.com/nexus/content/groups/public/' }
        maven { url "https://artifact.bytedance.com/repository/pangle/" }
    }
}
```

---

## Step 2. SDK 초기화

Unity 플러그인은 `AdMixer` MonoBehaviour를 통해 Android 네이티브 브릿지를 호출합니다.

```csharp
public class AdMixer : MonoBehaviour
{
    public string androidMediaKey;
    public string androidBannerAdUnitId;
    public string androidInterstitialAdUnitId;
    public string androidNativeAdUnitId;
    public string androidRewardAdUnitId;
    public string androidVideoAdUnitId;

    void Start()
    {
        // SDK 초기화 (사용할 AdUnit ID 목록 전달)
        bridge.CallStatic("initSdk", activity, androidMediaKey, adUnitList);
    }
}
```

---

## 배너 광고

### 광고 로드

```csharp
AdMixer.Instance.LoadBanner();
```

### 광고 노출

```csharp
AdMixer.Instance.ShowBanner();
```

### 광고 제거

```csharp
AdMixer.Instance.DestroyBanner();
```

### 생명주기 연결

```csharp
AdMixer.Instance.BannerOnPause();   // OnApplicationPause(true) 시
AdMixer.Instance.BannerOnResume();  // OnApplicationPause(false) 시
```

### 이벤트 리스너

```csharp
public class AdMixerAdListener : MonoBehaviour
{
    public void OnReceivedAd(string param)
    {
        Debug.Log("[Banner] Load Success: " + param);
    }
    public void OnFailedToReceiveAd(string param)
    {
        Debug.Log("[Banner] Load Fail: " + param);
    }
    public void OnEventAd(string param)
    {
        Debug.Log("[Banner] Event: " + param);
    }
}
```

---

## 전면 배너 광고 (Interstitial)

### 광고 로드

```csharp
AdMixer.Instance.LoadInterstitial();
```

### 광고 노출

```csharp
AdMixer.Instance.ShowInterstitial();
```

### 광고 제거

```csharp
AdMixer.Instance.DestroyInterstitial();
```

### 이벤트 리스너

```csharp
public class AdMixerAdListener : MonoBehaviour
{
    public void OnReceivedAd(string param)
    {
        Debug.Log("[Interstitial] Load Success: " + param);
    }
    public void OnFailedToReceiveAd(string param)
    {
        Debug.Log("[Interstitial] Load Fail: " + param);
    }
    public void OnEventAd(string param)
    {
        Debug.Log("[Interstitial] Event: " + param);
        if (param == "CLOSE")
        {
            Debug.Log("[Interstitial] Closed");
        }
    }
}
```

---

## 네이티브 광고

네이티브 광고는 레이아웃 선택 옵션이 있습니다.

```csharp
[Tooltip("true = item_320x480, false = item_320x100 (Android Native 전용)")]
public bool androidNativeUseLargeLayout = true;
```

### 광고 로드

```csharp
AdMixer.Instance.LoadNativeAd();
```

### 광고 제거

```csharp
AdMixer.Instance.DestroyNativeAd();
```

### 이벤트 리스너

```csharp
public class AdMixerAdListener : MonoBehaviour
{
    public void OnReceivedAd(string param)
    {
        Debug.Log("[Native] Load Success: " + param);
    }
    public void OnFailedToReceiveAd(string param)
    {
        Debug.Log("[Native] Load Fail: " + param);
    }
    public void OnEventAd(string param)
    {
        Debug.Log("[Native] Event: " + param);
    }
}
```

---

## 리워드 동영상 광고

### 광고 로드

```csharp
AdMixer.Instance.LoadRewardVideo();
```

### 광고 노출

```csharp
AdMixer.Instance.ShowRewardVideo();
```

### 광고 제거

```csharp
AdMixer.Instance.DestroyRewardVideo();
```

### 이벤트 리스너

```csharp
public class AdMixerAdListener : MonoBehaviour
{
    public void OnReceivedAd(string param)
    {
        Debug.Log("[Reward] Load Success: " + param);
    }
    public void OnFailedToReceiveAd(string param)
    {
        Debug.Log("[Reward] Load Fail: " + param);
    }
    public void OnEventAd(string param)
    {
        Debug.Log("[Reward] Event: " + param);
        if (param == "EARNEDREWARD")
        {
            // 여기서 보상 지급 처리
            Debug.Log("[Reward] Reward Earned");
        }
        if (param == "COMPLETION")
        {
            Debug.Log("[Reward] Playback Complete");
        }
        if (param == "SKIPPED")
        {
            Debug.Log("[Reward] Skipped");
        }
        if (param == "CLOSE")
        {
            Debug.Log("[Reward] Closed");
        }
    }
}
```

---

## 동영상 광고 (Video)

### 광고 로드

```csharp
AdMixer.Instance.LoadVideoAd();
```

### 광고 제거

```csharp
AdMixer.Instance.DestroyVideoAd();
```

### 이벤트 리스너

```csharp
public class AdMixerAdListener : MonoBehaviour
{
    public void OnReceivedAd(string param)
    {
        Debug.Log("[Video] Load Success: " + param);
    }
    public void OnFailedToReceiveAd(string param)
    {
        Debug.Log("[Video] Load Fail: " + param);
    }
    public void OnEventAd(string param)
    {
        Debug.Log("[Video] Event: " + param);
        if (param == "COMPLETION")
        {
            Debug.Log("[Video] Playback Complete");
        }
        if (param == "SKIPPED")
        {
            Debug.Log("[Video] Skipped");
        }
    }
}
```
