# NAP Tracking SDK 개요

NAP Tracking SDK는 앱 내 사용자 행동 이벤트를 수집하고 분석하기 위한 경량 SDK입니다.

## 주요 기능

| 기능 | 설명 |
|------|------|
| 이벤트 트래킹 | 커스텀 이벤트 이름 + 속성(properties) 수집 |
| 화면 추적 | 화면 전환 자동/수동 추적 |
| 사용자 속성 | `identify`로 사용자 프로필 속성 설정 |
| 크래시 리포팅 | 앱 크래시 자동 감지 및 전송 |
| 클릭 자동 추적 | View 계층 기반 클릭 이벤트 자동 수집 |
| 배치 전송 | 이벤트를 모아 주기적으로 서버 전송 (메모리/파일 큐) |

## 이벤트 수집 구조

```
앱 이벤트 → GYTracking API
         → CoreTracker → QueueManager → MemoryQueue / PersistQueue
                                     → UploadScheduler → 서버 전송
RUM 자동 수집 (Crash / Screen / Click) → CoreTracker
```

## 시작하기

- [Android →](android/getting-started)
- [iOS →](ios/getting-started)

## 문의

- 이메일: [nap_adx@nasmedia.co.kr](mailto:nap_adx@nasmedia.co.kr)
