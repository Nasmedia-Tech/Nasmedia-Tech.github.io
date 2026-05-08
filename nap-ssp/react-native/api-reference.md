# React Native - API 레퍼런스

`react-native-nap-ssp` 플러그인의 전체 API 명세입니다.

---

## NapSspAd (Core)

SDK 초기화 및 전역 설정을 관리합니다.

### `NapSspAd.initialize(config)`

앱 시작 시 **한 번만** 호출합니다.

```tsx
NapSspAd.initialize({
  mediaKey: 'YOUR_MEDIA_KEY',
  adUnitIds: ['BANNER_ID', 'NATIVE_ID', 'INTERSTITIAL_ID', 'REWARD_ID'],
  logLevel: 'debug',  // 'verbose' | 'debug' | 'info' | 'warn' | 'error' | 'none'
});
```

| 파라미터 | 타입 | 설명 |
|---|---|---|
| `mediaKey` | `string` | 파트너 사이트에서 발급받은 미디어 키 |
| `adUnitIds` | `string[]` | 사용할 모든 광고 단위 ID 목록 |
| `logLevel` | `string` | 로그 레벨 (기본값: `'warn'`) |
| `mediations` | `MediationConfig` | 미디에이션 네트워크별 앱 ID / 키 |

### `NapSspAd.getStatus()`

현재 SDK 초기화 상태 및 로드된 광고 정보를 반환합니다.

### `NapSspAd.requestTrackingAuthorization()` *(iOS 전용)*

ATT(App Tracking Transparency) 권한 요청을 수행합니다.

---

## BannerAd (Component)

### Props

| Prop | 타입 | 설명 |
|---|---|---|
| `adUnitId` | `string` | 광고 단위 ID |
| `size` | `BannerSize` | 배너 크기 |
| `onAdLoaded` | `() => void` | 광고 로드 성공 |
| `onAdFailedToLoad` | `(error: AdError) => void` | 광고 로드 실패 |
| `onAdClicked` | `() => void` | 광고 클릭 |
| `onAdImpression` | `() => void` | 광고 노출 |
| `style` | `ViewStyle` | 뷰 크기 및 스타일 |

### BannerSize 상수

| 상수 | 크기 |
|---|---|
| `BANNER_320x50` | 320 × 50 |
| `BANNER_320x100` | 320 × 100 |
| `BANNER_300x250` | 300 × 250 |
| `BANNER_320x480` | 320 × 480 |
| `LARGE_BANNER` | 320 × 100 |
| `MEDIUM_RECTANGLE` | 300 × 250 |
| `SMART_BANNER` | 화면 너비 자동 조절 |

---

## NativeAd (Component)

### Props

| Prop | 타입 | 설명 |
|---|---|---|
| `adUnitId` | `string` | 광고 단위 ID |
| `onAdLoaded` | `() => void` | 광고 로드 성공 |
| `onAdFailedToLoad` | `(error: AdError) => void` | 광고 로드 실패 |
| `onAdClicked` | `() => void` | 광고 클릭 |
| `onAdImpression` | `() => void` | 광고 노출 |
| `style` | `ViewStyle` | **`width`/`height` 필수** |

---

## VideoAd (Component)

### Props

`NativeAd`의 모든 Props 포함, 추가로:

| Prop | 타입 | 설명 |
|---|---|---|
| `onAdCompleted` | `() => void` | 동영상 시청 완료 |
| `onAdSkipped` | `() => void` | 동영상 스킵 |
| `isRetry` | `boolean` | 실패 시 재시도 여부 *(Android 전용)* |

---

## InterstitialAd (Class)

```tsx
const interstitial = new InterstitialAd('AD_UNIT_ID');
```

### Methods

| 메서드 | 반환 | 설명 |
|---|---|---|
| `load()` | `Promise<void>` | 서버에서 광고 로드 |
| `show()` | `Promise<void>` | 광고 표시 |
| `addAdEventListener(event, handler)` | `void` | 이벤트 리스너 등록 |

### 이벤트

| 이벤트 | 설명 |
|---|---|
| `loaded` | 광고 로드 완료 |
| `loadFailed` | 광고 로드 실패 |
| `opened` | 광고 열림 |
| `closed` | 광고 닫힘 |
| `clicked` | 광고 클릭 |
| `impression` | 광고 노출 |

---

## InterstitialVideoAd (Class)

`InterstitialAd`와 동일한 메서드 구조, 추가 이벤트:

| 이벤트 | 설명 |
|---|---|
| `completed` | 동영상 시청 완료 |
| `skipped` | 동영상 스킵 |

---

## RewardedAd (Class)

```tsx
const rewarded = new RewardedAd('AD_UNIT_ID');
```

### Methods

`InterstitialAd`와 동일. 추가로:

```tsx
rewarded.customParams = {
  useid: 'user123',   // S2S 콜백 파라미터 (선택)
  name: '홍길동',
};
```

### 이벤트

`InterstitialAd` 이벤트 포함, 추가로:

| 이벤트 | 설명 |
|---|---|
| `rewarded` | 보상 지급 — 여기서 리워드 지급 처리 |
| `completed` | 동영상 시청 완료 |
| `skipped` | 동영상 스킵 |

---

## AdError 타입

| 필드 | 타입 | 설명 |
|---|---|---|
| `code` | `string` | 에러 구분 코드 |
| `message` | `string` | 상세 에러 메시지 |
| `nativeCode` | `number?` | 네이티브 SDK 원본 에러 코드 |
