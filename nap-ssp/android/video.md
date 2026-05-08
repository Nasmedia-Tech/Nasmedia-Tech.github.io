# Android - 동영상 광고

> 동영상 광고 추가 전 [시작하기](getting-started) Step 1~3이 완료되었는지 확인하세요.  
> Interstitial 형태는 [배너 - 전면 배너](banner#전면-배너-광고-interstitial-banner)를 참고하세요.

## 인라인 동영상 광고 (VideoAdView)

```java
public class VideoActivity extends AppCompatActivity {
    private VideoAdView videoAdView;
    private RelativeLayout container;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_video);
        container = findViewById(R.id.container_video);

        AdInfo adInfo = new AdInfo.Builder(Application.ADUNIT_ID_VIDEO)
            .isRetry(false)  // false: 1회 요청 후 즉시 콜백
            .build();

        videoAdView = new VideoAdView(this);
        videoAdView.setAdInfo(adInfo);
        videoAdView.setAdViewListener(new AdListener() {
            @Override
            public void onReceivedAd(String adapterName, Object adView) {
                RelativeLayout.LayoutParams params = new RelativeLayout.LayoutParams(
                    ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.WRAP_CONTENT);
                params.addRule(RelativeLayout.CENTER_IN_PARENT);
                container.removeView(videoAdView);
                container.addView(videoAdView, params);
            }
            @Override
            public void onFailedToReceiveAd(Object adView, String adapterName, int errorCode, String errorMsg) {}
            @Override
            public void onEventAd(Object o, AdEvent adEvent) {
                switch (adEvent) {
                    case COMPLETION: // 재생 완료
                    case SKIPPED:    // Skip 클릭
                    case CLICK:      // 더보기 클릭
                        break;
                }
            }
        });
        videoAdView.loadAd();
    }

    @Override protected void onResume() { if (videoAdView != null) videoAdView.onResume(); super.onResume(); }
    @Override protected void onPause() { if (videoAdView != null) videoAdView.onPause(); super.onPause(); }
    @Override protected void onDestroy() { if (videoAdView != null) { videoAdView.onDestroy(); videoAdView = null; } super.onDestroy(); }
}
```

---

## 전면 동영상 광고 (InterstitialVideoAd)

```java
public class InterstitialVideoActivity extends AppCompatActivity {
    private InterstitialVideoAd interstitialVideoAd;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        AdInfo adInfo = new AdInfo.Builder(Application.ADUNIT_ID_INTERSTITIAL_VIDEO)
            .interstitialTimeout(0)   // 0: 서버 지정 (기본 20초)
            .maxRetryCountInSlot(-1)  // -1: 무한 재시도
            .build();

        interstitialVideoAd = new InterstitialVideoAd(this);
        interstitialVideoAd.setAdInfo(adInfo);
        interstitialVideoAd.setListener(new AdListener() {
            @Override
            public void onReceivedAd(Object o) {}
            @Override
            public void onFailedToReceiveAd(Object o, int i, String s) {}
            @Override
            public void onEventAd(Object o, AdEvent adEvent) {
                switch (adEvent) {
                    case CLOSE:
                    case SKIPPED:
                        interstitialVideoAd.closeInterstitialVideoAd();
                        break;
                    case COMPLETION:
                    case CLICK:
                        break;
                }
            }
        });

        Button btnShow = findViewById(R.id.btn_show);
        btnShow.setOnClickListener(v -> {
            if (interstitialVideoAd.hasInterstitial)
                interstitialVideoAd.showInterstitialVideoAd();
            else
                interstitialVideoAd.loadInterstitialVideoAd();
        });
    }

    @Override
    protected void onDestroy() {
        if (interstitialVideoAd != null) {
            interstitialVideoAd.stopInterstitialVideoAd();
            interstitialVideoAd.setListener(null);
            interstitialVideoAd = null;
        }
        super.onDestroy();
    }
}
```
