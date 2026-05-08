# Android - 배너 광고

> 배너 광고 추가 전 [시작하기](getting-started) Step 1~3이 완료되었는지 확인하세요.

## 배너 광고 (Banner)

배너 광고는 두 가지 방법으로 노출할 수 있습니다.

| 방법 | 설명 |
|------|------|
| 방법 1 | 광고 수신 즉시 자동 노출 — `container.addView(banner)` |
| 방법 2 | 원하는 시점에 노출 — `loadAd()` 후 `showAd()` 수동 호출 |

### 코드 방식 예제

```java
public class BannerActivity extends AppCompatActivity {
    private RelativeLayout container;
    private AdView banner;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_banner);
        container = findViewById(R.id.container_banner);

        // 미디에이션 어댑터 (사용하는 네트워크만 등록)
        AdMixer.registerAdapter(AdMixer.ADAPTER_ADMANAGER);
        AdMixer.registerAdapter(AdMixer.ADAPTER_ADFIT);
        AdMixer.registerAdapter(AdMixer.ADAPTER_PANGLE);
        AdMixer.registerAdapter(AdMixer.ADAPTER_APPLOVIN);
        AdMixer.registerAdapter(AdMixer.ADAPTER_UNITY);

        AdInfo adInfo = new AdInfo.Builder(Application.ADUNIT_ID_BANNER)
            .setIsUseMediation(true)  // 미디에이션 필수
            .build();

        RelativeLayout.LayoutParams params = new RelativeLayout.LayoutParams(
            ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.WRAP_CONTENT);

        banner = new AdView(this);
        banner.setLayoutParams(params);
        banner.setAdInfo(adInfo);
        banner.setAlwaysShowAdView(false);  // 광고 로딩 전 영역 차지 여부
        banner.setAdViewListener(new AdListener() {
            @Override
            public void onReceivedAd(String adapterName, Object adView) {
                // 방법 2: 원하는 시점에 노출
                // if (banner.hasAd) {
                //     container.removeView(banner);
                //     container.addView(banner);
                //     banner.showAd();
                // }
            }
            @Override
            public void onFailedToReceiveAd(Object adView, String adapterName, int errorCode, String errorMsg) {}
            @Override
            public void onEventAd(Object o, AdEvent adEvent) {
                switch (adEvent) {
                    case CLICK:     // 클릭
                    case DISPLAYED: // 노출
                        break;
                }
            }
        });

        // 방법 1: 즉시 노출
        container.addView(banner);
    }

    @Override protected void onResume() { if (banner != null) banner.onResume(); super.onResume(); }
    @Override protected void onPause() { if (banner != null) banner.onPause(); super.onPause(); }
    @Override protected void onDestroy() { if (banner != null) banner.onDestroy(); super.onDestroy(); }
}
```

### Layout XML 방식

```xml
<!-- activity_banner.xml -->
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.nasmedia.admixer.ads.AdView
        android:id="@+id/banner"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_centerHorizontal="true" />
</RelativeLayout>
```

> **주의**: Adfit 사용 시 `AdView(this)` 는 Activity context 필수입니다. `getApplicationContext()` 는 Adfit에서 지원하지 않습니다.

---

## 전면 배너 광고 (Interstitial Banner)

### 전면 광고 형태

| 형식 | 설명 |
|------|------|
| `basic` | 우측 상단 X 버튼 |
| `popup` | 소재 하단 텍스트 버튼. 배경색 커스터마이징 가능 |
| `countDown` | 설정 시간 후 닫기 버튼 (2~5초, gauge/text 타입) |

### 팝업형 전면 광고 예제

```java
public class InterstitialActivity extends AppCompatActivity {
    InterstitialAd interstitialAd;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        AdMixer.registerAdapter(AdMixer.ADAPTER_ADMANAGER);
        AdMixer.registerAdapter(AdMixer.ADAPTER_ADFIT);
        AdMixer.registerAdapter(AdMixer.ADAPTER_APPLOVIN);
        AdMixer.registerAdapter(AdMixer.ADAPTER_PANGLE);
        AdMixer.registerAdapter(AdMixer.ADAPTER_UNITY);

        PopupInterstitialAdOption adConfig = new PopupInterstitialAdOption();
        adConfig.setDisableBackKey(false);           // 뒤로가기 버튼 방지
        adConfig.setButtonLeft("광고종료", "#234234"); // 닫기 버튼 (필수)
        adConfig.setButtonRight("오른쪽버튼", null);  // 앱 종료 버튼 (선택)
        adConfig.setCountDown(0, 5);                 // gauge 타입, 5초

        AdInfo adInfo = new AdInfo.Builder(Application.ADUNIT_ID_INTERSTITIAL_BANNER)
            .isUseBackgroundAlpha(true)
            .popupAdOption(adConfig)
            .interstitialAdType(AdInfo.InterstitialAdType.Popup)
            .setIsUseMediation(true)
            .build();

        interstitialAd = new InterstitialAd(this);
        interstitialAd.setAdInfo(adInfo);
        interstitialAd.setAdListener(new AdListener() {
            @Override
            public void onReceivedAd(String adapterName, Object adView) {}
            @Override
            public void onFailedToReceiveAd(Object adView, String adapterName, int errorCode, String errorMsg) {}
            @Override
            public void onEventAd(Object o, AdEvent adEvent) {
                switch (adEvent) {
                    case LEFT_CLICK:  // 왼쪽 버튼 클릭
                    case RIGHT_CLICK: // 오른쪽 버튼 클릭
                    case CLOSE:       // 닫힘
                    case DISPLAYED:   // 노출
                        break;
                }
            }
        });

        // 원하는 시점에 노출
        Button btnShow = findViewById(R.id.btn_show);
        btnShow.setOnClickListener(v -> {
            if (interstitialAd.hasInterstitial)
                interstitialAd.showInterstitial();
            else
                interstitialAd.loadInterstitial();
        });
    }

    @Override
    protected void onDestroy() {
        if (interstitialAd != null) {
            interstitialAd.stopInterstitial();
            interstitialAd = null;
        }
        super.onDestroy();
    }
}
```
