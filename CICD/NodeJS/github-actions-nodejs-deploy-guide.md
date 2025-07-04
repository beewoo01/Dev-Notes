# GitHub Actions + Ubuntu 서버로 Node.js 자동 배포 구축기

## 1. 왜 자동 배포를 구축하게 되었는가

Node.js로 실시간 소켓 서버를 운영하면서 가장 귀찮았던 일 중 하나는 **배포 작업이었다**.
코드 수정 후마다 매번 SSH로 서버에 접속해 `git pull`, `npm install`, `pm2 restart`를 반복하다 보면,
언제나 똑같은 명령어를 타이핑하고 있는 나 자신이 너무 비효율적으로 느껴졌다.

또한 이런 수동 배포는 항상 다음과 같은 문제를 동반했다:

* 무중단 배포가 어렵다 (접속 중단 가능성)
* git pull을 빼먹고 재시작해 에러가 발생한다
* 작업자가 바뀌면 매뉴얼 공유가 필요하다

그래서 다음 목표를 세웠다:

> "코드만 Push하면, 서버에 자동으로 반영되고 서비스는 중단 없이 유지되도록 만들자."

---

## 2. 전체 자동 배포 구조 요약

내가 구축한 자동 배포 시스템은 다음과 같이 작동한다:

```plaintext
[로컬 개발] → git push →
[GitHub Actions 실행] →
[우분투 서버 SSH 접속] →
[코드 pull + npm ci + pm2 reload] →
[자동 배포 완료]
```

사용한 도구:

* Ubuntu 22.04 (카페24 가상서버)
* GitHub Actions
* SSH Key 기반 인증
* pm2 (Node.js 무중단 프로세스 관리 도구)

---

## 3. 서버 초기 세팅

```bash
# Node.js 설치
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs

# pm2 설치
sudo npm install -g pm2

# git 설치
sudo apt install git
```

서버 경로는 `/srv/OOO_server`로 설정했고, `OOO_server.js`가 메인 실행 파일이었다.

---

## 4. SSH Key 생성 및 GitHub 설정

**우분투 서버에서 SSH Key 생성:**

```bash
ssh-keygen -t ed25519 -C "deploy@OOOOO(key name)"
```
여기서 -C "deploy@OOOOO(key name)" 는 코멘트다.

생성된 공개키 `~/.ssh/id_ed25519.pub`는 GitHub 저장소 → `Settings` → `Deploy keys`에 등록
비공개키 `~/.ssh/id_ed25519` 내용은 GitHub `Secrets`에 등록한다.
* 하단 참고

---

## 5. GitHub Secrets 설정

GitHub 저장소의 `Settings > Secrets and variables > Actions`에서 다음을 등록:

| 이름            | 내용                                             |
| ------------- | ----------------------------------------------- |
| `HOST`        | 서버 IP (예: 1.123.22.789)                        |
| `USERNAME`    | root 또는 접근시 사용하는 user name                   |
| `PRIVATE_KEY` | `id_ed25519` 전체 내용 (-----BEGIN \~ -----END 포함) |

---

## 6. GitHub Actions 파일 작성

`.github/workflows/deploy.yml` 생성

```yaml
name: Deploy Node.js to Ubuntu Server

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v3

    - name: Deploy via SSH
      uses: appleboy/ssh-action@v1.0.3
      with:
        host: ${{ secrets.HOST }}
        username: ${{ secrets.USERNAME }}
        key: ${{ secrets.PRIVATE_KEY }}
        script: |
          cd /srv/weplhit-socket-server
          git pull origin main
          npm ci
          pm2 reload nodeapp || pm2 start weplhit_server.js --name nodeapp
```

---

## 7. 자동 배포 테스트

로컬에서 아래처럼 커밋만 해도:

```bash
git add .
git commit -m "update code"
git push origin main
```

→ GitHub Actions가 실행되고, 서버에서 코드가 pull + 설치 + 재시작까지 완료된다.

확인은 다음으로 가능:

```bash
# 서버에서
pm2 logs nodeapp
curl http://localhost:3000
```

---

## 8. 트러블슈팅 팁 (실제 겪은 문제들)

* `ssh: no key found`: PRIVATE_KEY에 `BEGIN` \~ `END` 포함하지 않아서 발생
* `ssh: handshake failed`: GitHub deploy key 등록 안 했거나 권한 부족
* `ECONNREFUSED`: 서버에서 Node.js가 안 떠있거나 포트 막혀있음

---

## 9. 마무리

이번 자동 배포 구축을 통해 실제로 배포가 훨씬 편리해졌고,
버전 반영 실수, SSH 접속 스트레스, 수동 재시작 문제까지 전부 해결할 수 있었다.

이제는 코드만 잘 작성하면 된다.



좋아, 그럼 **GitHub Actions 자동 배포**에 있어서 중요한 세 가지 설정:
**Deploy Keys, Secrets and Variables** 각각을 어떻게 쓰는지 명확하게 설명해줄게.
블로그용으로 바로 써도 될 만큼 깔끔하게 구성해줄게 😊

---



## 🔐 Deploy Keys, Secrets

자동 배포를 위해 GitHub과 원격 서버(Ubuntu)를 연결하려면 인증 정보와 환경 정보 설정이 필요하다.
이 과정에서 사용되는 것이 바로 **Deploy Key**, **Secrets** 다.

---

### 1️⃣ Deploy Key (배포용 공개키)

#### ✅ 역할

* **GitHub → 서버로 코드 클론 허용**
* 로컬 서버에서 `git clone`, `git pull` 등을 할 수 있게 만듦

#### ✅ 구성

* GitHub에는 **공개키(.pub)** 를 등록
* Ubuntu 서버에는 **비공개키**가 존재 (이걸로 인증)

#### ✅ 등록 방법

1. Ubuntu 서버에서 키 생성

   ```bash
   ssh-keygen -t ed25519 -C "deploy@yourserver"
   ```

   👉 `~/.ssh/id_ed25519.pub` (공개키)
   👉 `~/.ssh/id_ed25519` (비공개키)

2. GitHub → 리포지토리 → **Settings → Deploy Keys → Add Key**

   * Title: `Ubuntu Deploy Key`
   * Key: `cat ~/.ssh/id_ed25519.pub` 결과 붙여넣기
   * ✅ Allow write access 체크

---

### 2️⃣ Secrets (민감한 값 암호화 저장)

#### ✅ 역할

* GitHub Actions에서 사용되는 **비밀 정보** 저장용
* 서버 접속 정보, 비공개 키 등 **절대 공개되면 안 되는 값** 포함

#### ✅ 사용 예시

* `HOST`: 배포 대상 서버 IP
* `USERNAME`: root 혹은 ubuntu 등 SSH 사용자명
* `PRIVATE_KEY`: `~/.ssh/id_ed25519` 내용 (비공개키 전체)

#### ✅ 등록 방법

1. GitHub → **Settings → Secrets and variables → Actions → New repository secret**
2. 다음과 같이 3개 등록:

   ```
   Name: HOST
   Value: 1.2.3.4       ← 서버 IP

   Name: USERNAME
   Value: root          ← Ubuntu 서버의 사용자

   Name: PRIVATE_KEY
   Value: -----BEGIN OPENSSH PRIVATE KEY-----로 시작하는 키 전체
   ```

> ✅ `PRIVATE_KEY`는 반드시 `-----BEGIN~END-----`까지 포함해서 붙여넣어야 함

---

## 📦 요약 비교

| 항목             | 쓰임                 | 저장되는 곳               | 등록 예시                        |
| -------------- | ------------------ | -------------------- | ---------------------------- |
| **Deploy Key** | GitHub → 서버 인증     | GitHub → Deploy Keys | 공개키 붙여넣기                     |
| **Secrets**    | 민감 정보 (SSH, Key 등) | GitHub → Secrets     | PRIVATE\_KEY, HOST, USERNAME |

---

* “Deploy Key는 **GitHub 입장**에서 SSH 허용을 해주는 문”
* “Secrets는 **GitHub Actions 입장**에서 민감한 설정값 저장소”


