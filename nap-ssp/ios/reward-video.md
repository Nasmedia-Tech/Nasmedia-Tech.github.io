# iOS - 리워드 동영상 광고

> 리워드 동영상 광고 추가 전 [시작하기](getting-started) Step 1~3이 완료되었는지 확인하세요.

리워드 동영상 광고는 광고 요청 후 받은 뒤 **원하는 시점에 노출**합니다.

## 코드 예제

```swift
import AdMixerMediation

class RewardVideoViewController: UIViewController {
    var rewardVideo: AMMRewardVideo?

    override func viewDidLoad() {
        super.viewDidLoad()

        // S2S Reward Callback용 커스텀 파라미터 (선택)
        let customParam = [
            "useid": "user123",
            "name": "홍길동"
        ]

        AMMRewardVideo.load(adUnitID: "ADUNIT_ID", customParam: customParam) { [weak self] reward, error in
            guard let self = self else { return }
            if let error = error { print("Error: \(error)"); return }
            self.rewardVideo = reward
            self.rewardVideo?.delegate = self
        }
    }

    // 원하는 시점에 노출
    @IBAction func showRewardAd(_ sender: Any) {
        rewardVideo?.show(rootViewController: self)
    }

    override func viewDidDisappear(_ animated: Bool) {
        super.viewDidDisappear(animated)
        if isMovingFromParent || isBeingDismissed {
            rewardVideo?.stop()
            rewardVideo = nil
        }
    }
}
```

## Delegate

```swift
extension RewardVideoViewController: AMMRewardVideoDelegate {
    func onSuccessShowReward() {
        // 리워드 동영상 광고 노출 성공
    }
    func onFailShowReward(error: Error?) {
        // 리워드 동영상 광고 노출 실패
    }
    func onCloseRewardVideo() {
        // 광고 닫기
    }
    func onTapRewardVideo() {
        // 광고 탭
    }
    func onRewardVideoComplete() {
        // 동영상 재생 완료
    }
    func onRewardVideoEarned() {
        // 리워드 지급 — 여기서 리워드 지급 처리!
        grantReward()
    }
}
```

> 광고 네트워크별로 이벤트 제공 여부가 상이할 수 있습니다. 일부 네트워크는 재생 완료 이벤트를 미제공합니다.

---

## S2S Reward Callback (선택)

매체사 서버로 리워드 지급 완료 여부를 전달합니다. 콜백 수신까지 수 분 지연될 수 있습니다.

### 설정 방법

1. **파트너 사이트** → 미디어 관리 → 애드유닛 광고 설정에서 콜백 서버 URL 입력
2. SDK CustomParam 추가 (선택):

```swift
rewardVideo?.customParam = [
    "useid": "user123",
    "name": "홍길동"
]
```

### 기본 파라미터 (자동 포함)

| 파라미터 | 설명 |
|---------|------|
| `media_key` | 미디어 키 |
| `adunit_id` | 애드유닛 ID |
| `ifa` | iOS 기기 고유 식별자 (IDFA) |
| `timestamp` | 리워드 지급 이벤트 발생 시간 |

### 콜백 URL 예시

```
{콜백URL}?media_key={mediakey}&adunit_id={adunitid}&ifa={ifa}&timestamp={timestamp}&useid=user123
```
