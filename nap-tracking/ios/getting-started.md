# iOS Tracking SDK - 시작하기

> 문의: [nap_adx@nasmedia.co.kr](mailto:nap_adx@nasmedia.co.kr)

?> **출시 준비 중입니다.** 정식 배포 전 상세 연동 가이드가 이 페이지에 업데이트됩니다. 사전 도입 문의는 운영팀으로 연락주세요.

## 요구사항

- **최소 OS**: iOS 13.0 이상
- **최소 Xcode**: 15.0 이상
- **언어**: Swift

---

## SDK 기능 개요

| 기능 | 설명 |
|------|------|
| 이벤트 트래킹 | 커스텀 이벤트 이름 + 속성(properties) 수집 |
| 화면 추적 | UIViewController 수명주기 기반 자동 추적 |
| 사용자 속성 | 사용자 프로필 속성 설정 (`identify`) |
| 크래시 리포팅 | 앱 크래시 자동 감지 및 전송 |
| 배치 전송 | 이벤트를 모아 주기적으로 서버 전송 |

---

## API 설계 (예정)

### 초기화

```swift
// AppDelegate didFinishLaunchingWithOptions에서 1회 호출
NapTracking.initialize(
    apiKey: "발급받은 API Key"
    // 상세 옵션은 정식 배포 시 공개
)
```

### 이벤트 트래킹

```swift
// 커스텀 이벤트
NapTracking.track(name: "button_click")
NapTracking.track(name: "purchase", props: ["item_id": "product_123", "price": 9900])

// 화면 전환
NapTracking.screen(name: "MainScreen")

// 사용자 속성
NapTracking.identify(attrs: ["user_id": "user_abc", "plan": "premium"])
```

---

!> 위 코드는 예정된 API 설계이며, 실제 패키지명·메서드명은 정식 배포 시 변경될 수 있습니다.
