# Android - 네이티브 광고

> 네이티브 광고 추가 전 [시작하기](getting-started) Step 1~3이 완료되었는지 확인하세요.

## 구성 요소

네이티브 광고는 6가지 asset으로 구성됩니다. 각 asset을 사용해 자유롭게 UI를 구성할 수 있습니다.

| Asset | 설명 |
|-------|------|
| 제목 (`title`) | 광고 제목 텍스트 |
| 아이콘 (`icon`) | 광고주 아이콘 이미지 |
| 광고주 (`advertiser`) | 광고주명 텍스트 |
| 설명 (`description`) | 광고 설명 텍스트 |
| 메인 (`mainView`) | 이미지 또는 동영상 |
| 버튼 (`cta`) | Call-To-Action 버튼 |

> Admixer 단독 사용 시 title / icon / mainView 중 1개 이상 필수 사용해야 합니다.

---

## 레이아웃 XML

```xml
<!-- item_native.xml -->
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#f7f7f7">

    <ImageView
        android:id="@+id/iv_icon"
        android:layout_width="90dp"
        android:layout_height="90dp"
        android:layout_margin="12dp" />

    <TextView
        android:id="@+id/tv_title"
        android:layout_width="match_parent"
        android:layout_height="68dp"
        android:layout_marginStart="12dp"
        android:layout_marginTop="19dp"
        android:layout_toEndOf="@id/iv_icon"
        android:textSize="15sp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/tv_adv"
        android:layout_width="match_parent"
        android:layout_height="45dp"
        android:layout_below="@id/tv_title"
        android:layout_toEndOf="@id/iv_icon"
        android:textColor="#c7c7c7"
        android:textSize="11sp" />

    <TextView
        android:id="@+id/tv_desc"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@id/iv_icon"
        android:textColor="#2a2a2a"
        android:textSize="11sp" />

    <com.nasmedia.admixerssp.common.nativeads.NativeMainAdView
        android:id="@+id/iv_main"
        android:layout_width="match_parent"
        android:layout_height="250dp"
        android:layout_below="@id/tv_desc">
        <ImageView
            android:id="@+id/imageView_ad"
            android:layout_width="320dp"
            android:layout_height="250dp" />
    </com.nasmedia.admixerssp.common.nativeads.NativeMainAdView>

    <Button
        android:id="@+id/btn_cta"
        android:layout_width="match_parent"
        android:layout_height="56dp"
        android:layout_below="@id/iv_main"
        android:background="#2a2a2a"
        android:textColor="#ffffff" />
</RelativeLayout>
```

---

## Activity 예제

```java
public class NativeActivity extends AppCompatActivity {
    private NativeAdView nativeAdView;
    private RelativeLayout container;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_native);
        container = findViewById(R.id.container_native);

        AdMixer.registerAdapter(AdMixer.ADAPTER_ADMANAGER);
        AdMixer.registerAdapter(AdMixer.ADAPTER_ADFIT);
        AdMixer.registerAdapter(AdMixer.ADAPTER_PANGLE);

        // View ID 매핑 (Google, Adfit, Pangle 공통)
        Map<String, Integer> adViewIds = new HashMap<>();
        adViewIds.put("nativeLayout", R.layout.item_native);
        adViewIds.put("iv_icon", R.id.iv_icon);
        adViewIds.put("tv_title", R.id.tv_title);
        adViewIds.put("tv_adv", R.id.tv_adv);
        adViewIds.put("tv_desc", R.id.tv_desc);
        adViewIds.put("iv_main", R.id.iv_main);
        adViewIds.put("btn_cta", R.id.btn_cta);

        AdInfo adInfo = new AdInfo.Builder(Application.ADUNIT_ID_NATIVE)
            .setIsUseMediation(true)
            .setViewIds(AdMixer.ADAPTER_ADMANAGER, adViewIds)
            .setViewIds(AdMixer.ADAPTER_ADFIT, adViewIds)
            .setViewIds(AdMixer.ADAPTER_PANGLE, adViewIds)
            .build();

        NativeAdViewBinder viewBinder = new NativeAdViewBinder.Builder(R.layout.item_native)
            .setIconImageId(R.id.iv_icon)
            .setTitleId(R.id.tv_title)
            .setAdvertiserId(R.id.tv_adv)
            .setDescriptionId(R.id.tv_desc)
            .setMainViewId(R.id.iv_main)
            .setCtaId(R.id.btn_cta)
            .build();

        nativeAdView = new NativeAdView(this);
        nativeAdView.setAdInfo(adInfo);
        nativeAdView.setViewBinder(viewBinder);  // 필수
        nativeAdView.setAdViewListener(new AdListener() {
            @Override
            public void onReceivedAd(String adapterName, Object adView) {
                if (nativeAdView.hasAd) {
                    container.removeAllViews();
                    container.addView(nativeAdView);
                }
            }
            @Override
            public void onFailedToReceiveAd(Object adView, String adapterName, int errorCode, String errorMsg) {}
            @Override
            public void onEventAd(Object o, AdEvent adEvent) {
                switch (adEvent) {
                    case CLICK:
                    case DISPLAYED:
                        break;
                }
            }
        });
        nativeAdView.loadNativeAd();
    }

    @Override protected void onResume() { if (nativeAdView != null) nativeAdView.onResume(); super.onResume(); }
    @Override protected void onPause() { if (nativeAdView != null) nativeAdView.onPause(); super.onPause(); }
    @Override
    protected void onDestroy() {
        if (nativeAdView != null) { nativeAdView.onDestroy(); nativeAdView = null; }
        super.onDestroy();
    }
}
```

> Adfit 사용 시 `NativeAdView(this)` 는 Activity context 필수입니다.
