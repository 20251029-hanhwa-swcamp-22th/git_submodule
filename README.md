# Git Submodule 구성 가이드


Git Submodule은 **다른 Git Repository를 현재 Repository 내부에 폴더처럼 연결하여 관리하는 기능**입니다.

주로 다음과 같은 경우에 사용됩니다.

* Backend / Frontend / Infra 저장소를 분리하여 관리
* 여러 Repository를 하나의 프로젝트처럼 묶어 관리하고 싶은 경우

---

# 1. Git Submodule이란?

Git Submodule은 **다른 Git Repository를 현재 Repository 내부에 연결하는 기능**입니다.

예를 들어 아래와 같은 프로젝트 구조가 있다고 가정합니다.

```
project-manifest
 ├ backend
 ├ frontend
 └ infra
```

하지만 실제 구조는 다음과 같습니다.

```
backend   → 다른 Git Repository
frontend  → 다른 Git Repository
infra     → 다른 Git Repository
```

즉, **폴더처럼 보이지만 실제로는 외부 Repository를 참조하는 구조**입니다.

---

# 2. 사전 준비

Submodule을 구성하려면 다음이 필요합니다.

1. Git 설치
2. GitHub 계정
3. 최소 2개 이상의 Repository

예시 Repository

```
backend-repo
frontend-repo
infra-repo
manifest-repo
```

* `backend-repo` : 백엔드 코드
* `frontend-repo` : 프론트엔드 코드
* `infra-repo` : 배포 및 인프라 설정
* `manifest-repo` : 모든 Repository를 묶는 메인 저장소

---

# 3. Submodule 구성 방법

## 1️⃣ 메인 Repository 생성

GitHub에서 메인 Repository를 생성합니다.

예시

```
manifest-repo
```

로컬로 clone 합니다.

```bash
git clone https://github.com/your-org/manifest-repo.git
cd manifest-repo
```

---

## 2️⃣ Submodule 추가

다른 Repository를 Submodule로 추가합니다.

기본 명령어

```bash
git submodule add [Repository URL] [폴더명]
```

예시

```bash
git submodule add https://github.com/your-org/backend-repo.git backend
git submodule add https://github.com/your-org/frontend-repo.git frontend
git submodule add https://github.com/your-org/infra-repo.git infra
```

구조가 다음과 같이 생성됩니다.

```
manifest-repo
 ├ backend
 ├ frontend
 ├ infra
 └ .gitmodules
```

`.gitmodules` 파일에는 Submodule 정보가 저장됩니다.

---

## 3️⃣ Submodule 등록 커밋

Submodule 추가 후 변경사항을 commit 합니다.

```bash
git add .
git commit -m "Add submodules"
git push origin main
```

---

# 4. Submodule이 포함된 Repository clone 방법

Submodule이 포함된 Repository는 일반 clone과 다르게 받아야 합니다.

### 방법 1 (권장)

```bash
git clone --recurse-submodules [repository-url]
```

예시

```bash
git clone --recurse-submodules https://github.com/your-org/manifest-repo.git
```

---

### 방법 2 (이미 clone한 경우)

```bash
git submodule update --init --recursive
```

---

# 5. Submodule 내부에서 작업하는 방법

Submodule은 **독립된 Git Repository**입니다.

예를 들어 backend 코드를 수정하려면 다음과 같이 작업합니다.

```
manifest-repo
 └ backend
```

backend 폴더로 이동합니다.

```bash
cd backend
```

코드를 수정합니다.

```bash
git add .
git commit -m "Update backend feature"
git push origin main
```

---

# 6. Submodule 변경사항을 메인 Repository에 반영

Submodule 내부 commit이 변경되면
메인 Repository는 **참조하는 commit 위치가 변경**됩니다.

따라서 메인 Repository에서도 commit이 필요합니다.

상위 폴더로 이동합니다.

```bash
cd ..
```

변경된 Submodule을 commit 합니다.

```bash
git add backend
git commit -m "Update backend submodule"
git push origin main
```

---

# 7. Submodule 상태 확인

현재 Submodule 상태 확인

```bash
git submodule status
```

출력 예시

```
a1b2c3d backend
e4f5g6h frontend
i7j8k9l infra
```

각 Submodule이 참조하고 있는 commit 정보를 확인할 수 있습니다.

---

# 8. Submodule 최신 상태 가져오기

Remote 저장소 기준 최신 commit으로 업데이트

```bash
git submodule update --remote
```

변경 후에는 메인 Repository에서 다시 commit 합니다.

```bash
git add .
git commit -m "Update submodules"
git push origin main
```

---

# 9. Submodule 제거 방법

Submodule을 제거하려면 다음 단계를 수행합니다.

1️⃣ Submodule 등록 해제

```bash
git submodule deinit -f [submodule-path]
```

예시

```bash
git submodule deinit -f backend
```

2️⃣ Submodule 삭제

```bash
git rm backend
```

3️⃣ commit

```bash
git commit -m "Remove backend submodule"
```

---

# 10. Submodule 정보 파일

Submodule 정보는 `.gitmodules` 파일에 저장됩니다.

예시

```
[submodule "backend"]
    path = backend
    url = https://github.com/your-org/backend-repo.git

[submodule "frontend"]
    path = frontend
    url = https://github.com/your-org/frontend-repo.git
```

---

# 11. Submodule 사용 시 주의사항

### 1️⃣ 일반 폴더가 아님

Submodule은 일반 폴더가 아니라 **다른 Git Repository를 참조하는 링크**입니다.

---

### 2️⃣ 두 번 commit 해야 할 수 있음

Submodule에서 작업할 경우

```
1. submodule 내부 commit/push
2. 메인 repository에서 submodule 포인터 commit
```

이 과정이 필요합니다.

---

### 3️⃣ clone 시 submodule 옵션 필요

Submodule이 있는 Repository는 반드시 아래 방식으로 clone 하는 것이 좋습니다.

```
git clone --recurse-submodules
```

---

# 12. Submodule 사용 사례

Git Submodule은 다음과 같은 프로젝트 구조에서 많이 사용됩니다.

```
project-manifest
 ├ backend
 ├ frontend
 ├ mobile
 └ infra
```

각 영역을 **독립적인 Repository로 관리하면서도 하나의 프로젝트처럼 구성**할 수 있습니다.

---

# 13. 참고 자료

Git 공식 문서

https://git-scm.com/book/en/v2/Git-Tools-Submodules

---
