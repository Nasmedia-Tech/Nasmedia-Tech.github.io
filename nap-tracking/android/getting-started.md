# Android Tracking SDK - 시작하기

> 문의: [nap_adx@nasmedia.co.kr](mailto:nap_adx@nasmedia.co.kr)

?> **출시 준비 중입니다.** 정식 배포 전 상세 연동 가이드가 이 페이지에 업데이트됩니다. 사전 도입 문의는 운영팀으로 연락주세요.

## 요구사항

- **최소 OS**: Android 5.0 (API 21) 이상
- **언어**: Kotlin / Java

---

## SDK 기능 개요

| 기능 | 설명 |
|------|------|
| 이벤트 트래킹 | 커스텀 이벤트 이름 + 속성(properties) 수집 |
| 화면 추적 | 화면 전환 자동/수동 추적 |
| 사용자 속성 | 사용자 프로필 속성 설정 (`identify`) |
| 크래시 리포팅 | 앱 크래시 자동 감지 및 전송 |
| 클릭 자동 추적 | View 계층 기반 클릭 이벤트 자동 수집 |
| 배치 전송 | 이벤트를 모아 주기적으로 서버 전송 |

---

## API 설계 (예정)

### 초기화

```kotlin
// Application.onCreate()에서 1회 호출
NapTracking.initialize(
    context = this,
    apiKey = "발급받은 API Key",
    // 상세 옵션은 정식 배포 시 공개
)
```

### 이벤트 트래킹

```kotlin
// 커스텀 이벤트
NapTracking.track("button_click")
NapTracking.track("purchase", mapOf("item_id" to "product_123", "price" to 9900))

// 화면 전환
NapTracking.screen("MainScreen")

// 사용자 속성
NapTracking.identify(mapOf("user_id" to "user_abc", "plan" to "premium"))
```

### 클릭 자동 추적

```kotlin
// Activity rootView 전달 시 자동 수집
NapTracking.enableClickTracking(window.decorView.rootView)
```

---

!> 위 코드는 예정된 API 설계이며, 실제 패키지명·메서드명은 정식 배포 시 변경될 수 있습니다.
