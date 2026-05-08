# iOS - 배너 광고

> 배너 광고 추가 전 [시작하기](getting-started) Step 1~3이 완료되었는지 확인하세요.

## 배너 광고 (AMMBannerView)

배너 광고는 광고 요청 후 즉시 노출하는 방식을 지원합니다.

### 인스턴스 생성 및 설정

```swift
import AdMixerMediation

class BannerViewController: UIViewController {
    var bannerView: AMMBannerView!

    override func viewDidLoad() {
        super.viewDidLoad()

        bannerView = AMMBannerView(rootViewController: self)
        bannerView.adUnitId = "ADUNIT_ID"
        bannerView.delegate = self

        // Auto Layout으로 뷰 추가
        bannerView.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(bannerView)
        NSLayoutConstraint.activate([
            bannerView.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            bannerView.trailingAnchor.constraint(equalTo: view.trailingAnchor),
            bannerView.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor),
        ])

        // 광고 요청
        bannerView.load()
    }

    override func viewDidDisappear(_ animated: Bool) {
        super.viewDidDisappear(animated)
        if isMovingFromParent || isBeingDismissed {
            bannerView.stop()
            bannerView = nil
        }
    }
}
```

### Delegate

```swift
extension BannerViewController: AMMBannerViewDelegate {
    func onSuccessBanner() {
        // 배너 광고 로드 성공
    }
    func onFailBanner() {
        // 배너 광고 로드 실패
    }
    func onTapBanner() {
        // 배너 광고 탭
    }
}
```

---

## 전면 배너 광고 (AMMInterstitial)

전면 배너는 광고 요청 후 받은 뒤 **원하는 시점에 노출**합니다.

### 전면 광고 형태

| 형식 | 설명 |
|------|------|
| `basic` | 우측 상단 X 버튼 |
| `popup` | 소재 하단 텍스트 버튼. `popupOption`으로 커스터마이징 가능 |
| `countDown` | 설정 시간 후 닫기 버튼 (2~5초, gauge/text 타입) |

### 인스턴스 생성 및 광고 요청

```swift
class InterstitialViewController: UIViewController {
    var interstitial: AMMInterstitial?

    override func viewDidLoad() {
        super.viewDidLoad()

        let config = AMMInterstitialConfig()
        config.viewType = .popup
        config.popupOption = AMMInterstitialPopupOption(
            buttonTitle: "닫기",
            buttonTextColor: .white,
            buttonBackgroundColor: .black
        )
        config.countDownOption = AMMInterstitialCountDownOption(
            countDownTime: 4,
            countDownType: .gauge
        )
        config.closeButtonTouchAreaRatio = 1.0  // 0.2~1.0

        AMMInterstitial.load(adUnitID: "ADUNIT_ID", config: config) { [weak self] interstitial, error in
            guard let self = self else { return }
            if let error = error { print("Error: \(error)"); return }
            self.interstitial = interstitial
            self.interstitial?.delegate = self
        }
    }

    // 원하는 시점에 노출
    func showAd() {
        interstitial?.show(rootViewController: self)
    }

    override func viewDidDisappear(_ animated: Bool) {
        super.viewDidDisappear(animated)
        if isMovingFromParent || isBeingDismissed {
            interstitial?.stop()
            interstitial = nil
        }
    }
}
```

### Delegate

```swift
extension InterstitialViewController: AMMInterstitialDelegate {
    func onSuccessShowInterstitial() { /* 노출 성공 */ }
    func onFailShowInterstitial(error: Error?) { /* 노출 실패 */ }
    func onTapInterstitial() { /* 탭 */ }
    func onCloseInterstitial() { /* 닫기 */ }
}
```
