# 🚀 GitHub Actions

## 📌 개요

**GitHub Actions**는 Flutter 프로젝트의 빌드, 테스트, 린트, 배포 과정을 자동화해주는 GitHub 내장 CI/CD 도구입니다.  
수동 반복 작업을 줄이고, 코드 품질을 일정하게 유지하며, 협업 효율을 높이는 데 사용됩니다.

---

## ✅ 왜 사용하는가?

| 이유 | 설명 |
|------|------|
| ⏱ 반복 작업 자동화 | `flutter build`, `flutter test` 등을 자동 실행 |
| ❌ 실수 방지 | 디버그 빌드, 테스트 누락 등 방지 |
| 📦 품질 관리 | PR마다 자동 테스트/코드 분석 수행 |
| 🤝 협업 개선 | 누구나 같은 방식으로 빌드/배포 |
| 🧠 추적 가능성 | 어떤 커밋이 어떤 APK를 생성했는지 기록 가능 |

---

## 🔧 기본 구조

```yaml
.github/
  workflows/
    flutter_ci.yml    # GitHub Actions 워크플로 파일
name: Flutter CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Setup Flutter
        uses: subosito/flutter-action@v2
        with:
          flutter-version: '3.13.0'

      - name: Install dependencies
        run: flutter pub get

      - name: Analyze code
        run: flutter analyze

      - name: Run tests
        run: flutter test
```


## 🚀 자동 배포 방식

자동 배포는 GitHub Actions에서 APK를 빌드한 뒤, 특정 플랫폼으로 자동 전송하는 과정을 의미합니다.  
배포 대상과 목적에 따라 방식이 달라집니다.

| 목적 | 방식 | 설명 |
|------|------|------|
| 테스트 사용자에게 전달 | **Firebase App Distribution** | `GitHub Actions`에서 빌드한 APK를 Firebase로 업로드하고, 테스터에게 이메일/링크로 전달 |
| 정식 사용자 대상 배포 | **Google Play Store + Fastlane** | 버전 태그(예: `v1.2.0`)를 푸시하면 `fastlane`을 통해 Play Console에 APK 자동 업로드 |
| APK 파일만 저장 | **GitHub Releases** | APK를 GitHub의 Release 탭에 업로드하여 공유용 다운로드 링크 제공 |

## 🔄 일반적인 자동화 흐름

Flutter 프로젝트에서 GitHub Actions를 통한 자동화는 다음과 같은 흐름으로 구성됩니다.

1. **PR 또는 커밋 푸시**
2. GitHub Actions가 자동으로:
   - `flutter pub get` 실행
   - `flutter analyze`로 코드 스타일 검사
   - `flutter test`로 테스트 수행
   - `flutter build apk`로 APK 생성
3. **테스트 및 빌드 성공 시**:
   - Firebase App Distribution 또는 GitHub Releases에 자동 업로드
4. **main 브랜치에 태그(`v1.0.0`)를 푸시하면**:
   - Fastlane을 통해 Google Play Store에 자동 배포

> 이 흐름은 반복 작업을 줄이고, 실수를 방지하며, 모든 빌드 결과가 코드 기반으로 추적 가능하게 해줍니다.

---

## 🧰 사용되는 주요 액션

| 액션 | 설명 |
|------|------|
| `actions/checkout@v3` | 저장소의 코드를 내려받습니다. |
| `subosito/flutter-action@v2` | 지정된 버전의 Flutter SDK를 설치합니다. |
| `wzieba/Firebase-Distribution-Github-Action@v1` | Firebase App Distribution에 APK를 업로드합니다. |
| `softprops/action-gh-release@v1` | APK를 GitHub Releases에 업로드합니다. |
| `codecov/codecov-action@v3` | 테스트 커버리지 리포트를 Codecov에 전송합니다. |
| `fastlane` (로컬 실행) | Google Play Store나 TestFlight에 앱을 자동 업로드합니다. |

> 이 액션들을 조합해서 다양한 자동화 시나리오를 구성할 수 있으며, 필요에 따라 확장 가능합니다.

---

### 🔧 예시 액션들

```yaml
# Firebase App Distribution
- name: Upload to Firebase
  uses: wzieba/Firebase-Distribution-Github-Action@v1
  with:
    appId: ${{ secrets.FIREBASE_APP_ID }}
    token: ${{ secrets.FIREBASE_AUTH_TOKEN }}
    groups: testers
    file: build/app/outputs/flutter-apk/app-release.apk

# GitHub Release 업로드
- name: Upload APK to GitHub Release
  uses: softprops/action-gh-release@v1
  with:
    files: build/app/outputs/flutter-apk/app-release.apk

# Play Store 업로드 (Fastlane)
- name: Deploy to Play Store
  run: |
    cd android
    bundle exec fastlane deploy

