# Android NAPTracker SDK 연동 가이드

> 문의: [nap_adx@nasmedia.co.kr](mailto:nap_adx@nasmedia.co.kr)

## 개요

NAPTracker SDK는 사용자 행동을 추적하고 분석하기 위한 라이브러리입니다. 이 가이드를 통해 SDK를 앱에 손쉽게 연동하고 주요 기능을 사용하는 방법을 익힐 수 있습니다.

- 샘플 앱: [android-nap-tracker-quickstart](https://github.com/Nasmedia-Tech/android-nap-tracker-quickstart)

---

## 1. SDK 설치

### Step 1-1. 저장소 설정

프로젝트 최상위 `settings.gradle.kts` 파일에 Maven 저장소를 추가합니다. (이미 설정되어 있으면 생략)

```kotlin
// settings.gradle.kts
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
    }
}
```

### Step 1-2. 의존성 추가

앱 모듈의 `build.gradle.kts`에 의존성을 추가합니다.

```kotlin
// build.gradle.kts (app)
dependencies {
    implementation("io.github.nasmedia-tech:nap-tracker:0.0.6") // 최신 버전으로 변경하세요
}
```

### Step 1-3. AndroidManifest.xml 권한 설정

`app/src/main/AndroidManifest.xml`에 인터넷 및 광고 ID 접근 권한을 추가합니다.

```xml
<manifest>
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.gms.permission.AD_ID" />

    <application>
        ...
    </application>
</manifest>
```

---

## 2. SDK 초기화

앱의 `Application` 클래스 `onCreate()`에서 SDK를 초기화합니다.

```kotlin
import android.app.Application
import com.nasmedia.naptracker.presentation.NapTracker

class MyApplication : Application() {
    override fun onCreate() {
        super.onCreate()

        // NapTracker SDK 초기화
        NapTracker.initialize(this)
    }
}
```

---

## 3. 주요 기능

### 3-1. 사용자 ID 설정 (선택)

로그인 등 사용자를 식별할 수 있는 시점에 호출합니다.

```kotlin
NapTracker.setUserId("your-user-id")
```

### 3-2. 사용자 속성 설정 (선택)

사용자의 특정 속성(레벨, 성별 등)을 저장합니다.

```kotlin
NapTracker.setUserProperty("gender", "male")
NapTracker.setUserProperty("level", "5")
```

### 3-3. 이벤트 로깅

사용자 행동을 추적하기 위해 `logEvent()`를 사용합니다.

**파라미터 없는 이벤트:**

```kotlin
NapTracker.logEvent("screen_view")
```

**파라미터 있는 이벤트:**

```kotlin
import android.os.Bundle

val params = Bundle().apply {
    putString("item_id", "product-123")
    putString("item_name", "Awesome T-shirt")
    putDouble("price", 29.99)
}

NapTracker.logEvent("add_to_cart", params)
```
