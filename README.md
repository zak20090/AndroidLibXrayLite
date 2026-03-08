# AndroidLibXrayLite

## Build requirements
* JDK
* Android SDK
* Go
* gomobile

## Build instructions
1. `git clone [repo] && cd AndroidLibXrayLite`
2. `gomobile init`
3. `go mod tidy -v`
4. `gomobile bind -v -androidapi 21 -ldflags='-s -w' ./`

## Receiving Xray logs in Java / Kotlin

Xray console log lines are forwarded to the `CoreCallbackHandler` interface via
`OnEmitStatus(int code, String msg)`.  The `code` value distinguishes the type
of status event:

| `code` | Meaning |
|--------|---------|
| `0`    | Lifecycle status message (e.g. "Started successfully, running", "Core stopped") |
| `1`    | Xray console log line |

### Example (Kotlin)

```kotlin
class MyHandler : CoreCallbackHandler {
    override fun startup(): Int = 0
    override fun shutdown(): Int = 0

    override fun onEmitStatus(code: Int, msg: String): Int {
        when (code) {
            0 -> Log.i("VPN", "Status: $msg")
            1 -> Log.d("XrayLog", msg)   // Xray console log line
        }
        return 0
    }
}

val controller = Libv2ray.newCoreController(MyHandler())
```

Existing `OnEmitStatus(0, ...)` calls (lifecycle messages) are unaffected.
