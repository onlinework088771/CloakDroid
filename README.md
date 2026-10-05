# CloakDroid

CloakDroid is an Android GeckoView browser prototype for privacy-oriented,
profile-based browsing and authorized compatibility testing. It does **not**
promise anonymity, undetectability, complete device emulation, or leak-free
operation without device testing.

## Current implementation status

- Kotlin, Jetpack Compose, GeckoView 128, Hilt, Room and OkHttp.
- Profile records, per-profile application data directories, bookmarks and
  history are implemented.
- HTTP/HTTPS and SOCKS5 proxy **testing** is implemented through OkHttp.
- Proxy input accepts separate fields and common pasted proxy URL formats.
- Invalid non-direct proxy configuration fails closed instead of silently
  becoming Direct.
- Proxy test results include IP/metadata when the external services respond.
- The browser currently owns one live GeckoSession at a time. Launching another
  profile closes the previous session; simultaneous independent profiles are
  not implemented.
- Gecko proxy preference application is best-effort and fail-closed, but
  browser egress has not been verified on a real device in this repository.
- SOCKS5 authentication, browser-level proxy egress, DNS/IPv6 leak behavior,
  WebSocket/download/service-worker routing, and Gecko storage isolation are
  not certified by the app.
- `ScriptInjector` is a payload builder only. It is not a document-start
  injection mechanism until a Gecko WebExtension/content-script integration is
  installed. It must not be treated as active fingerprint protection.
- Native Android permission state is never fabricated by the script payload.

## Build

The project requires JDK 17 and an Android SDK suitable for compileSdk 34.

```bash
./gradlew testDebugUnitTest lintDebug assembleDebug
```

GitHub Actions builds a debug APK and uploads it as an artifact. A successful
APK build does not prove proxy routing or privacy isolation; those require
controlled proxy fixtures and an Android device/emulator.

## Proxy input

Use separate fields or paste one of these formats into the Host field:

```text
host:port
http://username:password@host:port
https://username:password@host:port
socks5://username:password@host:port
```

Credentials are currently stored in the Room profile entity and are **not yet
Keystore-encrypted**. Do not use production credentials in this prototype.

## Privacy and testing limitations

The app currently has no evidence-based "undetectability" score. Diagnostics
must be interpreted as configuration status, not proof of anonymity. Before
claiming a proxy works for browser traffic, compare GeckoView egress with the
OkHttp tester using a controlled endpoint and test redirects, subresources,
WebSockets, downloads, DNS, IPv4/IPv6, and failure behavior.

## CI

`.github/workflows/build-apk.yml` currently assembles the debug APK. Device
and instrumentation tests are not yet part of the workflow.
