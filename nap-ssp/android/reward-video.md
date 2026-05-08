# Android - 리워드 동영상 광고

> 리워드 동영상 광고 추가 전 [시작하기](getting-started) Step 1~3이 완료되었는지 확인하세요.

## 노출 방법

| 방법 | 설명 |
|------|------|
| 방법 1 | `loadRewardVideoAd()` 후 `onReceivedAd`에서 즉시 `showRewardVideoAd()` |
| 방법 2 | `loadRewardVideoAd()` 후 `hasInterstitial` 확인 → 원하는 시점에 `showRewardVideoAd()` |

## 코드 예제

```java
public class RewardVideoActivity extends AppCompatActivity {
    private RewardInterstitialVideoAd rewardAd;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_reward);

        AdMixer.registerAdapter(AdMixer.ADAPTER_ADMANAGER);
        AdMixer.registerAdapter(AdMixer.ADAPTER_APPLOVIN);
        AdMixer.registerAdapter(AdMixer.ADAPTER_PANGLE);
        AdMixer.registerAdapter(AdMixer.ADAPTER_UNITY);

        // S2S Reward Callback용 커스텀 파라미터 (선택)
        Map<String, String> params = new HashMap<>();
        params.put("use_id", "user123");
        params.put("name", "홍길동");

        AdInfo adInfo = new AdInfo.Builder(Application.ADUNIT_ID_REWARD_VIDEO)
            .setCustomParams(params)  // 선택사항
            .setMute(true)            // 음소거 여부
            .setIsUseMediation(true)
            .build();

        rewardAd = new RewardInterstitialVideoAd(this);
        rewardAd.setAdInfo(adInfo);
        rewardAd.setListener(new AdListener() {
            @Override
            public void onReceivedAd(String adapterName, Object adView) {}
            @Override
            public void onFailedToReceiveAd(Object adView, String adapterName, int errorCode, String errorMsg) {}
            @Override
            public void onEventAd(Object adView, AdEvent adEvent) {
                switch (adEvent) {
                    case CLOSE:        // 광고 닫힘
                    case SKIPPED:      // Skip 버튼 클릭
                        break;
                    case COMPLETION:   // 재생 완료
                    case CLICK:        // 더보기 클릭
                        break;
                    case EARNEDREWARD: // 리워드 획득 — 여기서 리워드 지급!
                        grantReward();
                        break;
                }
            }
        });

        Button btnShow = findViewById(R.id.btn_show);
        btnShow.setOnClickListener(v -> {
            if (rewardAd.hasInterstitial)
                rewardAd.showRewardVideoAd();
            else
                rewardAd.loadRewardVideoAd();
        });
    }

    private void grantReward() {
        // 리워드 지급 로직
    }

    @Override
    protected void onDestroy() {
        if (rewardAd != null) {
            rewardAd.stopRewardVideoAd();
            rewardAd.setListener(null);
            rewardAd = null;
        }
        super.onDestroy();
    }
}
```

---

## S2S Reward Callback (선택)

리워드 지급 이벤트를 매체사 서버로 전달하는 기능입니다.

### 설정 방법

1. **파트너 사이트** → 미디어 관리 → 애드유닛 광고 설정에서 콜백 서버 URL 입력
2. SDK에서 커스텀 파라미터 추가 (선택)

### 기본 파라미터 (자동 포함)

| 파라미터 | 설명 |
|---------|------|
| `media_key` | 미디어 키 |
| `adunit_id` | 애드유닛 ID |
| `adid` | Google Advertising ID |
| `earnedreward` | 리워드 지급 이벤트 |
| `timestamp` | 이벤트 발생 시간 |

### 콜백 URL 예시

```
{콜백URL}?media_key={mediakey}&adunit_id={adunitid}&adid={adid}&timestamp={timestamp}&use_id=user123
```

> 광고 네트워크별로 리워드 지급 완료 시점이 상이할 수 있습니다.
