# Changelog

## [5.1.0](https://github.com/pieces-app/encrypt/compare/v5.0.4...v5.1.0) (2025-11-30)


### ✨ Features

* add support for OAEP SHA256 ([#263](https://github.com/pieces-app/encrypt/issues/263)) ([e95fbf4](https://github.com/pieces-app/encrypt/commit/e95fbf47b9c5843c902d4c5b20dcc6a5ff6b9141))
* null safety ([#195](https://github.com/pieces-app/encrypt/issues/195)) ([1c9aa8f](https://github.com/pieces-app/encrypt/commit/1c9aa8f12e8dbe57f9b5e561239f6ec0dbe6988a))
* optional padding (default pkcs7) ([3f86652](https://github.com/pieces-app/encrypt/commit/3f8665296bd1d157bce84547e72ae72cc7f7d987))
* optional padding (default pkcs7) ([6d4c12b](https://github.com/pieces-app/encrypt/commit/6d4c12b528023747aae746b270c703cd05619c79))


### 🐛 Bug Fixes

* 32 ([32d2091](https://github.com/pieces-app/encrypt/commit/32d20914adefa4b45e75948133a6dc5d6f7f3012))
* Could not parse version "~3.6.2" ([#315](https://github.com/pieces-app/encrypt/issues/315)) ([f92c4f1](https://github.com/pieces-app/encrypt/commit/f92c4f18c5b775a50a949da1e86c2f6e924edd89))


### ♻️ Refactoring

* remove deprecated author attr ([f409383](https://github.com/pieces-app/encrypt/commit/f409383c92892d7302afa52b3d6f860ddca74b5d))


### 🔧 Chores

* add CI/CD setup completion marker ([07bf815](https://github.com/pieces-app/encrypt/commit/07bf815cb1163eb5f418ea14df9e3ba87dec33ae))
* **deps:** bump ([8430a34](https://github.com/pieces-app/encrypt/commit/8430a34b5b62fef07cfa9e185a5b2fab1ce9b749))
* **docs:** add 5.0.0-beta.1 to changelog ([def73f6](https://github.com/pieces-app/encrypt/commit/def73f6a42f88d6b609b18fca7be87ee369838c8))
* **docs:** changelog ([bc2a3f4](https://github.com/pieces-app/encrypt/commit/bc2a3f44339574edb5c374b991b6386c495a1bbb))
* update pointy castle dep ([fcf1fe0](https://github.com/pieces-app/encrypt/commit/fcf1fe0c474262e121292f7d3eacb6319ac7bf1b))
* version bump, closes [#200](https://github.com/pieces-app/encrypt/issues/200) ([267e516](https://github.com/pieces-app/encrypt/commit/267e516367eb05896787d5f461dc2a7dd564c044))

## 5.0.4

- Force Pointycastle version

## 5.0.3

- Fixed tests failing with AES ECB with padding by @JimWuerch in #312
- fix: Could not parse version "~3.6.2" by @Marc-R2 in #315

## 5.0.2

- Update pointycastle version to support AES-GCM with Flutter Web
- Support AES-GCM
- Fixed null safety related warnings from `package:asnlib`.

## 5.0.1

- Fix web support

## 5.0.0

- Null safety support stable (sdk: ">=2.12.0 <3.0.0")

## 5.0.0-beta.1

- Preview/prerelase null safety support

## 4.1.0

- PointyCastle v2

## 4.0.3

- Fix UTF-8 conversion on Fernet keys.

## 4.0.2

- Fix streamble AES modes without padding.

## 4.0.1

- Upgrade dependencies.

## 4.0.0

- Digital signatures signing and verification.

## 3.3.1

- Move I/O helper to another lib

## 3.3.0

- Added the Fernet algorithm, thanks to [@timfeirg](https://github.com/timfeirg)
- Moved the secure random logic to the lib
- Added key stretching

## 3.2.0

- Fix wrong casting.
- Add decryptBytes, avoids UTF-8 high coupling.
- Add public decryption and private encryption for digital signature verification.

## 3.1.0

- Add support for CRLF PEM keys.
- Fix AES without padding.
- Add `encryptBytes` method.

## 3.0.0

- Enforce IV uniqueness.

## 2.2.0

- AES padding is now optional with defaults to PKCS7.

## 2.1.0

- `secure-random` command-line tool.

## 2.0.0

- All new API

## 1.0.1

- RSA

## 1.0.0

- Stable and documented API

## 0.2.0+2

- Remove unnecessary `new`s
- Improve static typing
- Add examples index (README)

## 0.2.0+1

- Refresh dependencies, make sure it works on Dart 2
