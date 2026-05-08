# Android Tracking SDK - 시작하기

> 문의: [nap_adx@nasmedia.co.kr](mailto:nap_adx@nasmedia.co.kr)

## 요구사항

- **최소 OS**: Android 5.0 (API 21) 이상
- **언어**: Kotlin

---

## Step 1. 의존성 추가

`app/build.gradle.kts`:

```kotlin
dependencies {
    implementation("com.nasmedia:nap-tracking-android:1.0.0")
}
```

---

## Step 2. SDK 초기화

`Application.onCreate()`에서 1회 초기화합니다.

```kotlin
import com.gwangy.gwangytracking.api.GYTracking
import com.gwangy.gwangytracking.config.TrackingConfig
import com.gwangy.gwangytracking.util.LogLevel

class MyApplication : Application() {
    override fun onCreate() {
        super.onCreate()

        val config = TrackingConfig(
            apiKey = "발급받은 API Key",
            endpoint = "https://tracking.nasmedia.co.kr/collect",
            flushInterval = 30_000L,      // 이벤트 전송 주기 (ms)
            maxBatchSize = 50,            // 배치 최대 이벤트 수
            enableCrashTracking = true,   // 크래시 자동 수집
            logLevel = LogLevel.INFO,
            debug = false
        )

        GYTracking.initialize(this, config)
    }
}
```

---

## Step 3. 이벤트 추적

### 커스텀 이벤트

```kotlin
// 이벤트 이름만
GYTracking.track("button_click")

// 이벤트 + 속성
GYTracking.track("purchase", mapOf(
    "item_id" to "product_123",
    "price" to 9900,
    "currency" to "KRW"
))
```

### 화면 전환

```kotlin
GYTracking.screen("MainScreen")

GYTracking.screen("ProductDetailScreen", mapOf(
    "product_id" to "product_123"
))
```

### 사용자 속성

```kotlin
GYTracking.identify(mapOf(
    "user_id" to "user_abc",
    "plan" to "premium",
    "age" to 28
))
```

---

## Step 4. 클릭 자동 추적

Activity 또는 Fragment의 rootView를 전달합니다.

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // rootView의 모든 클릭 이벤트 자동 수집
        GYTracking.enableClickTracking(window.decorView.rootView)
    }
}
```

---

## 설정 옵션

| 옵션 | 타입 | 기본값 | 설명 |
|------|------|--------|------|
| `apiKey` | String | 필수 | 발급받은 API Key |
| `endpoint` | String | 필수 | 이벤트 수집 서버 URL |
| `flushInterval` | Long | 30000 | 이벤트 전송 주기 (ms) |
| `maxBatchSize` | Int | 50 | 배치 최대 이벤트 수 |
| `enableCrashTracking` | Boolean | false | 크래시 자동 수집 여부 |
| `logLevel` | LogLevel | INFO | 로그 레벨 (VERBOSE/DEBUG/INFO/WARN/ERROR) |
| `debug` | Boolean | false | 디버그 모드 |

---

## ProGuard 설정

```proguard
-keep class com.gwangy.gwangytracking.** { *; }
-keep class com.nasmedia.tracking.** { *; }
```
