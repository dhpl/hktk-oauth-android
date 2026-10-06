# HKTK OAuth SDK for Android

Android SDK đăng nhập HKTK dành cho Kotlin và Java. SDK hiển thị login dialog,
gọi HKTK Auth API và trả authorization code để game gửi về backend đổi token.

Phiên bản hiện tại: `1.3.12`. Android tối thiểu: API 23.

## Cài đặt

Thêm Maven repository vào `settings.gradle.kts`:

```kotlin
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        maven {
            url = uri("https://raw.githubusercontent.com/dhpl/hktk-oauth-android/main/releases")
        }
    }
}
```

Thêm dependency vào `app/build.gradle.kts`:

```kotlin
dependencies {
    implementation("vn.hktk:hktk-sdk:1.3.12")
}
```

Với Groovy Gradle:

```gradle
dependencies {
    implementation "vn.hktk:hktk-sdk:1.3.12"
}
```

Đảm bảo app có quyền Internet:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## Sử dụng Kotlin

```kotlin
import vn.hktk.sdk.HKTKCallback
import vn.hktk.sdk.HKTKConfig
import vn.hktk.sdk.HKTKEnvironment
import vn.hktk.sdk.HKTKLoginResult
import vn.hktk.sdk.HKTKSDK

val config = HKTKConfig(
    environment = HKTKEnvironment.SANDBOX,
    clientId = "YOUR_CLIENT_ID",
    gameCode = "YOUR_GAME_CODE",
    redirectScheme = "mygame"
)

HKTKSDK.init(applicationContext, config)

HKTKSDK.showLogin(this, object : HKTKCallback {
    override fun onSuccess(result: HKTKLoginResult) {
        // Gửi code và redirectUri về backend game để đổi token.
        sendToBackend(result.code, result.redirectUri)
    }

    override fun onError(errorCode: String, message: String) {
        println("$errorCode: $message")
    }

    override fun onCancel() {
        println("User cancelled")
    }
})
```

Với cấu hình trên, redirect URI là:

```text
mygame://hktk-callback
```

Backend game phải dùng nguyên văn `redirectUri` SDK trả về khi đổi code. Không
đặt `clientSecret` trong app Android.

Tài liệu đầy đủ: https://docs.hktk.vn/oauth/hktk-auth-api.html
