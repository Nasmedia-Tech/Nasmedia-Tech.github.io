# iOS - 동영상 광고

> 동영상 광고 추가 전 [시작하기](getting-started) Step 1~3이 완료되었는지 확인하세요.  
> Interstitial 형태는 [배너 - 전면 배너 광고](banner#전면-배너-광고-amminterstitial)를 참고하세요.

## 인라인 동영상 광고 (AMMVideoView)

```swift
import AdMixerMediation

class VideoViewController: UIViewController {
    var ammVideoView: AMMVideoView!

    override func viewDidLoad() {
        super.viewDidLoad()

        ammVideoView = AMMVideoView(rootViewController: self)
        ammVideoView.adUnitID = "ADUNIT_ID"
        ammVideoView.delegate = self

        ammVideoView.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(ammVideoView)
        NSLayoutConstraint.activate([
            ammVideoView.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            ammVideoView.trailingAnchor.constraint(equalTo: view.trailingAnchor),
            ammVideoView.centerYAnchor.constraint(equalTo: view.centerYAnchor),
            ammVideoView.heightAnchor.constraint(equalToConstant: 200)
        ])

        ammVideoView.load()
    }

    override func viewDidDisappear(_ animated: Bool) {
        super.viewDidDisappear(animated)
        if isMovingFromParent || isBeingDismissed {
            ammVideoView.stop()
            ammVideoView = nil
        }
    }
}

extension VideoViewController: AMMVideoViewDelegate {
    func onSuccessVideo() { /* 로드 성공 */ }
    func onFailVideo() { /* 로드 실패 */ }
    func onSkipVideo() { /* Skip 버튼 클릭 */ }
    func onTapVideoViewMore() { /* 더보기 클릭 */ }
    func onCompleteVideo() { /* 재생 완료 */ }
}
```

---

## 전면 동영상 광고 (AMMVideoInterstitial)

> 전면 동영상 재생 시 Skip 버튼은 제공되지 않으며, Skip 가능한 시점에 닫기 버튼이 노출됩니다.

```swift
import AdMixerMediation

class VideoInterstitialViewController: UIViewController {
    var interstitial: AMMVideoInterstitial?

    override func viewDidLoad() {
        super.viewDidLoad()

        AMMVideoInterstitial.load(adUnitID: "ADUNIT_ID") { [weak self] videoInterstitial, error in
            guard let self = self else { return }
            if let error = error { print("Error: \(error)"); return }
            self.interstitial = videoInterstitial
            self.interstitial?.delegate = self
        }
    }

    @IBAction func showAd(_ sender: Any) {
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

extension VideoInterstitialViewController: AMMVideoInterstitialDelegate {
    func onSuccessShowVideoInterstitial() { /* 노출 성공 */ }
    func onFailShowVideoInterstitial(error: Error?) { /* 노출 실패 */ }
    func onCloseVideoInterstitial() { /* 닫기 */ }
    func onTapVideoInterstitialViewMore() { /* 더보기 클릭 */ }
    func onCompleteVideoInterstitial() { /* 재생 완료 */ }
}
```
