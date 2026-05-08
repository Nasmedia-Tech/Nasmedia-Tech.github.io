# iOS SDK - Unity 연동 가이드

> nap ssp SDK iOS Unity 플러그인 연동 가이드입니다. nap ssp Mediation을 지원합니다.  
> 문의: [nap_adx@nasmedia.co.kr](mailto:nap_adx@nasmedia.co.kr)

## 버전 기록

| 날짜 | 버전 | 변경사항 |
|------|------|---------|
| - | 1.0.1 | 최초 릴리즈 |

---

## Step 1. SDK 설치

### CocoaPods로 설치

Xcode 프로젝트 루트에서 Podfile을 생성합니다.

```bash
pod init
```

`Podfile`에 의존성을 추가합니다.

```ruby
target 'MyApp' do
  use_frameworks!
  pod 'AdMixerMediation'              # (필수) nap ssp Mediation
  pod 'AdMixerMediationGAM'           # Google AdManager
  pod 'AdMixerMediationAdFit'         # Kakao AdFit
  pod 'AdMixerMediationMobWith'       # MobWith
  pod 'AdMixerMediationPangle'        # Pangle
  pod 'AdMixerMediationAppLovin'      # AppLovin
  pod 'AdMixerMediationUnityAds'      # Unity Ads
end
```

```bash
pod update
```

---

## Step 2. SDK 초기화

Unity 씬에서 콜백을 수신할 GameObject 이름을 등록합니다.

```csharp
void Awake()
{
    gameObject.name = "NAPSSPPluginIOS";
    NAPSSPPluginIOS.SetUnityCallbackHandler("NAPSSPPluginIOS");
}
```

`Start()`에서 SDK를 초기화합니다.

```csharp
void Start()
{
    int[] adUnitIds = { ADUNIT_ID_BANNER, ADUNIT_ID_INTERSTITIAL, ADUNIT_ID_NATIVE };
    NAPSSPPluginIOS.Initialize(MEDIA_KEY, adUnitIds);

    // Google AdManager 초기화 (해당 네트워크 사용 시)
    NAPSSPPluginIOS.initGAM();

    // MobWith 초기화 (해당 네트워크 사용 시)
    NAPSSPPluginIOS.initMobWith("앱 ID");

    // Pangle 초기화 (해당 네트워크 사용 시)
    NAPSSPPluginIOS.initPangle("앱 ID");

    // AppLovin 초기화 (해당 네트워크 사용 시, SDK Key 필수)
    NAPSSPPluginIOS.initAppLovin("sdkKey");

    // UnityAds 초기화 (해당 네트워크 사용 시)
    NAPSSPPluginIOS.initUnityAds("앱 ID");
}
```

---

## 배너 광고

### 광고 초기화

고정 위치 (상단/하단):

```csharp
NAPSSPPluginIOS.BannerViewInit(ADUNIT_ID, NAPSSPPluginIOS.SSPPositionTop, 320, 50);
```

커스텀 좌표:

```csharp
NAPSSPPluginIOS.BannerViewInit(ADUNIT_ID, x, y, 320, 50);
```

### 광고 로드

```csharp
NAPSSPPluginIOS.BannerViewLoadAd();
```

### 이벤트 델리게이트

```csharp
public class MySampleScene : MonoBehaviour
{
    void Start()
    {
        gameObject.name = "MySampleScene";
        NAPSSPPluginIOS.SetUnityCallbackHandler("MySampleScene");

        NAPSSPPluginIOS.BannerViewSetDelegate();

        NAPSSPPluginIOS.OnSuccessBanner += OnBannerLoaded;
        NAPSSPPluginIOS.OnFailBanner    += OnBannerFailed;
        NAPSSPPluginIOS.OnTapBanner     += OnBannerTapped;
    }

    public void OnBannerLoaded() { }
    public void OnBannerFailed() { }
    public void OnBannerTapped() { }
}
```

---

## 전면 배너 광고 (Interstitial)

### 광고 초기화

```csharp
NAPSSPPluginIOS.InterstitialInit(ADUNIT_ID);
```

### 광고 로드

```csharp
NAPSSPPluginIOS.InterstitialLoadAd();
```

### 전면 광고 형태 설정 (선택)

```csharp
NAPSSPPluginIOS.InterstitialSetViewType(type);
```

### 이벤트 델리게이트

```csharp
public class MySampleScene : MonoBehaviour
{
    void Start()
    {
        gameObject.name = "MySampleScene";
        NAPSSPPluginIOS.SetUnityCallbackHandler("MySampleScene");

        NAPSSPPluginIOS.InterstitialSetDelegate();

        NAPSSPPluginIOS.OnSuccessInterstitial += OnInterstitialLoaded;
        NAPSSPPluginIOS.OnFailInterstitial    += OnInterstitialFailed;
        NAPSSPPluginIOS.OnCloseInterstitial   += OnInterstitialClosed;
        NAPSSPPluginIOS.OnTapInterstitial     += OnInterstitialTapped;
    }

    public void OnInterstitialLoaded() { }
    public void OnInterstitialFailed() { }
    public void OnInterstitialClosed() { }
    public void OnInterstitialTapped() { }
}
```

---

## 네이티브 광고

### 광고 초기화

고정 위치:

```csharp
NAPSSPPluginIOS.NativeViewInit(ADUNIT_ID, position, width, height);
```

커스텀 좌표:

```csharp
NAPSSPPluginIOS.NativeViewInit(ADUNIT_ID, x, y, width, height);
```

### 광고 로드

```csharp
NAPSSPPluginIOS.NativeViewLoadAd();
```

### 이벤트 델리게이트

```csharp
public class MySampleScene : MonoBehaviour
{
    void Start()
    {
        gameObject.name = "MySampleScene";
        NAPSSPPluginIOS.SetUnityCallbackHandler("MySampleScene");

        NAPSSPPluginIOS.NativeViewSetDelegate();

        NAPSSPPluginIOS.OnSuccessNative += OnNativeLoaded;
        NAPSSPPluginIOS.OnFailNative    += OnNativeFailed;
        NAPSSPPluginIOS.OnTapNative     += OnNativeTapped;
    }

    public void OnNativeLoaded() { }
    public void OnNativeFailed() { }
    public void OnNativeTapped() { }
}
```

---

## 리워드 동영상 광고

### 광고 초기화

```csharp
NAPSSPPluginIOS.RewardAdInit(ADUNIT_ID);
```

### 광고 로드

```csharp
NAPSSPPluginIOS.RewardLoadAd();
```

### S2S Reward Callback 파라미터 설정 (선택)

```csharp
NAPSSPPluginIOS.RewardAdSetCustomParam(new Dictionary<string, string>
{
    { "useid", "user123" },
    { "name",  "홍길동" },
    { "phone", "010-1111-1111" }
});
```

콜백 URL 예시:

```
{콜백URL}?media_key={mediakey}&adunit_id={adunitid}&ifa={adid}&timestamp={timestamp}&useid=user123
```

### 이벤트 델리게이트

```csharp
public class MySampleScene : MonoBehaviour
{
    void Start()
    {
        gameObject.name = "MySampleScene";
        NAPSSPPluginIOS.SetUnityCallbackHandler("MySampleScene");

        NAPSSPPluginIOS.RewardAdSetDelegate();

        NAPSSPPluginIOS.OnSuccessRewardVideo  += OnRewardLoaded;
        NAPSSPPluginIOS.OnFailRewardVideo     += OnRewardFailed;
        NAPSSPPluginIOS.OnCloseRewardVideo    += OnRewardClosed;
        NAPSSPPluginIOS.OnTapRewardVideo      += OnRewardTapped;
        NAPSSPPluginIOS.OnRewardVideoComplete += OnRewardCompleted;
        NAPSSPPluginIOS.onRewardVideoEarned   += OnRewardEarned;  // 리워드 지급
    }

    public void OnRewardLoaded()    { }
    public void OnRewardFailed()    { }
    public void OnRewardClosed()    { }
    public void OnRewardTapped()    { }
    public void OnRewardCompleted() { }
    public void OnRewardEarned()    { /* 여기서 보상 지급 처리 */ }
}
```

---

## 동영상 광고

### 인라인 동영상 (VideoView)

#### 초기화

```csharp
NAPSSPPluginIOS.VideoViewInit(ADUNIT_ID, x, y, width, height);
```

#### 광고 로드

```csharp
NAPSSPPluginIOS.VideoViewLoadAd();
```

#### 이벤트 델리게이트

```csharp
public class MySampleScene : MonoBehaviour
{
    void Start()
    {
        gameObject.name = "MySampleScene";
        NAPSSPPluginIOS.SetUnityCallbackHandler("MySampleScene");

        NAPSSPPluginIOS.VideoViewSetDelegate();

        NAPSSPPluginIOS.OnSuccessVideo      += OnVideoLoaded;
        NAPSSPPluginIOS.OnFailVideo         += OnVideoFailed;
        NAPSSPPluginIOS.OnSkipVideo         += OnVideoSkipped;
        NAPSSPPluginIOS.OnTapVideoViewMore  += OnVideoTapped;
        NAPSSPPluginIOS.OnCompleteVideo     += OnVideoCompleted;
    }

    public void OnVideoLoaded()    { }
    public void OnVideoFailed()    { }
    public void OnVideoSkipped()   { }
    public void OnVideoTapped()    { }
    public void OnVideoCompleted() { }
}
```

---

### 전면 동영상 (VideoInterstitial)

#### 초기화

```csharp
NAPSSPPluginIOS.VideoInterstitialInit(ADUNIT_ID);
```

#### 광고 로드

```csharp
NAPSSPPluginIOS.VideoInterstitialLoadAd();
```

#### 이벤트 델리게이트

```csharp
public class MySampleScene : MonoBehaviour
{
    void Start()
    {
        gameObject.name = "MySampleScene";
        NAPSSPPluginIOS.SetUnityCallbackHandler("MySampleScene");

        NAPSSPPluginIOS.VideoViewSetDelegate();

        NAPSSPPluginIOS.OnSuccessVideoInterstitial  += OnVideoInterstitialLoaded;
        NAPSSPPluginIOS.OnFailVideoInterstitial     += OnVideoInterstitialFailed;
        NAPSSPPluginIOS.OnCloseVideoInterstitial    += OnVideoInterstitialClosed;
        NAPSSPPluginIOS.OnTapVideoInterstitialViewMore += OnVideoInterstitialTapped;
        NAPSSPPluginIOS.OnCompleteVideoInterstitial += OnVideoInterstitialCompleted;
    }

    public void OnVideoInterstitialLoaded()    { }
    public void OnVideoInterstitialFailed()    { }
    public void OnVideoInterstitialClosed()    { }
    public void OnVideoInterstitialTapped()    { }
    public void OnVideoInterstitialCompleted() { }
}
```
