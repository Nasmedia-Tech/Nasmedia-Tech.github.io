# iOS - 네이티브 광고

> 네이티브 광고 추가 전 [시작하기](getting-started) Step 1~3이 완료되었는지 확인하세요.

## 구성 요소

| Asset | 설명 |
|-------|------|
| 아이콘 (`icon`) | 광고주 아이콘 이미지 |
| 제목 (`headline`) | 광고 제목 텍스트 |
| 광고주 (`advertiser`) | 광고주명 텍스트 |
| 설명 (`description`) | 광고 설명 텍스트 |
| 미디어 (`Media`) | 이미지 또는 동영상 |
| 버튼 (`cta`) | Call-To-Action 버튼 |

> **필수**: 제목(headline), 아이콘(icon), 미디어(Media) 중 **1개 이상** 반드시 사용해야 합니다.

---

## Step 1. xib 파일 추가

사이즈별 권장 `AMMNativeAdView.xib` 파일을 프로젝트에 추가합니다.

---

## Step 2. 인스턴스 생성 및 광고 요청

```swift
import AdMixerMediation

class NativeViewController: UIViewController {
    var nativeAd: AMMNativeAdViewContainer!

    override func viewDidLoad() {
        super.viewDidLoad()

        let nibView = Bundle.main.loadNibNamed("AMMNativeAdView", owner: nil, options: nil)?.first
        let nativeAdView = nibView as? AMMNativeAdView

        nativeAd = AMMNativeAdViewContainer(rootViewController: self)
        nativeAd.nativeAdView = nativeAdView
        nativeAd.adUnitID = "ADUNIT_ID"
        nativeAd.delegate = self

        // 광고 요청
        nativeAd.load()
    }

    override func viewDidDisappear(_ animated: Bool) {
        super.viewDidDisappear(animated)
        if isMovingFromParent || isBeingDismissed {
            nativeAd.stop()
            nativeAd = nil
        }
    }
}
```

## Delegate

```swift
extension NativeViewController: AMMNativeDelegate {
    func onSuccessNative() {
        // 네이티브 광고 로드 성공 — 뷰를 레이아웃에 추가
        if let adView = nativeAd.nativeAdView {
            view.addSubview(adView)
        }
    }
    func onFailNative() {
        // 네이티브 광고 로드 실패
    }
    func onTapNative() {
        // 네이티브 광고 탭
    }
}
```
