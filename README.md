# 🧠 Dev Diary  
### Settings of IDE, Error Clears, What I Learn  
개발 환경 설정, 에러 해결, 그리고 배운 점을 기록하는 개인 다이어리입니다.  
---

## 📅 2025-11-05 — IntelliJ + Git 완전 초기화 및 복구 기록

### 🧩 배경
오늘은 IntelliJ 프로젝트(`OGV4`)가 GitHub 연결 문제로 완전히 꼬여서,  
처음부터 **Git 연동을 재설정하고, IntelliJ의 캐시 및 루트 디렉터리 설정을 복구**하는 과정을 진행했다.  
결국 GitHub 연동, 커밋/푸시, Copilot 연결까지 전부 복원 완료.

---

## ⚙️ 1. 문제 상황

- IntelliJ에서 Git 관련 기능이 꼬임  
  → 커밋 창이 계속 뜨거나, “Git 루트가 감지되지 않음” 오류 발생.  
- `.git` 디렉터리와 로컬 Git 연결이 충돌.  
- GitHub 저장소와 IntelliJ의 로컬 연동이 끊김.  
- `src` 폴더가 IntelliJ 내에서 인식되지 않음 (소스 루트 붕괴).

---

## 🔧 2. 해결 과정 요약

### (1) IntelliJ 내에서 Git 완전 초기화
1. **상단 메뉴 → Git → 원격 관리 / 연결 해제**
2. `.git` 폴더 직접 삭제 (`E:\....\.git`) //삭제할때 파일에서 보기란 -> 표시 -> 숨김을 체크해줘야함. <img width="331" height="497" alt="image" src="https://github.com/user-attachments/assets/1f4a44d5-3bbf-4061-9ed5-77f674caa6c9" />

3. IntelliJ 캐시 초기화  
   - `파일 → 캐시 무효화 / 다시 시작`  
   - `VCS 로그 캐시 및 색인 삭제` 체크
   - `파일 -> 설정 -> 버전관리 -> 디렉터리 매핑에서 연결돼있는 디렉터리(git)`을 빼줘야함
4. IDE 재시작

> 💬 이때 “디렉터리가 Git 루트로 등록되었지만 Git 저장소를 찾을 수 없습니다.” 메시지가 사라져야 함.

---

### (2) 터미널에서 새 Git 초기화
```bash
git init
git add .
git config --global --add safe.directory E:/....
````

> ⚠️ 오류:
> `fatal: detected dubious ownership in repository at ...`
> → 위 명령어로 해결 (Git 2.35 이후 보안 강화 정책으로, 수동으로 안전 디렉터리 등록 필요)
> `파일 -> 설정 -> 버전관리 -> 디렉터리 매핑에서 연결돼있는 디렉터리(git)`를 추가.(적용, 확인 후 캐시 무효화 재시작) 이걸 해야 우리가 Git Branch를 볼 수 있음

---

### (3) Git 사용자 정보 등록

```bash
git config --global user.name "your name"
git config --global user.email "your_email@example.com"
```

> 전역 설정이라 한 번만 하면 됨. 이후 모든 커밋에 자동 반영.

---

### (4) 커밋 및 원격 연결

```bash
git add .
git commit -m "첫 커밋"
git branch -M main
git remote add origin https://github.com/rhslxkd/OGV-Movie-Booking.git
git push -u origin main
```

> 브라우저 인증창이 뜨면 GitHub 계정으로 로그인.
> 완료 후 “branch 'main' set up to track 'origin/main'.” 메시지 확인.

---

### (5) IntelliJ에서 src / test 루트 재지정

1. **파일 → 프로젝트 구조(Project Structure) → 모듈(Modules)**
2. `src/main/java`를 **Sources**,
   `src/test/java`를 **Tests**로 설정
3. `.gradle`, `build` 폴더는 **Excluded**로 변경
4. `바꾸고싶은 프로젝트를 클릭후 다음으로 표시: 를 누르면 간단하게 변경 가능. <img width="695" height="41" alt="image" src="https://github.com/user-attachments/assets/838777e6-36df-4a2a-bebf-0fa86cd1c125" />


> ⚠️ 오류:
> “이미 모듈 OGV4.main에 속해 있습니다.” → 기존 루트가 중복 설정된 상태.
> → “모듈 OGV4.main” 삭제 후 다시 지정하면 정상 복구.<img width="231" height="189" alt="image" src="https://github.com/user-attachments/assets/545edd8a-0c36-4221-8135-273f34198ffd" />


---

### (6) IntelliJ 구조 복구 확인

* 왼쪽 프로젝트 트리에서 `src` 밑에
  `main`, `test`가 파란색으로 표시되면 정상.
* `build.gradle`에서 경로가 맞는지 재확인 후 Gradle 리로드.

---

## 🧩 3. GitHub 연동 확인

* `git status` → `working tree clean`
* `git push` → 정상 반영
* GitHub 웹페이지에 `src` 폴더 포함 여부 확인
* `OGV-Movie-Booking` 레포지토리에 커밋 이력 생성됨

---

## 🧠 4. IntelliJ + GitHub Copilot 연결

1. IntelliJ → `설정 → 플러그인 → 마켓플레이스`
2. `GitHub Copilot` 검색 후 설치
3. IDE 재시작 후 **Copilot 로그인 팝업** 허용
4. 브라우저로 GitHub 로그인 → “Authorize GitHub Copilot” 클릭
5. IntelliJ에 “Copilot Chat” 패널 자동 표시 확인

> ⚙️ Copilot Chat 창이 뜨면 연결 성공.
> Chat Exporter나 기타 부가 플러그인은 선택 사항.

---

## 🧱 5. .gitattributes 설정 (EOL 정규화)

CRLF 경고가 여러 번 발생했기 때문에, 다음 파일 추가로 해결:

```
/gradlew text eol=lf
*.bat text eol=crlf
*.jar binary
* text=auto eol=lf
```

이후 명령어 실행:

```bash
git add --renormalize .
git status
```

결과: `nothing to commit, working tree clean` → EOL 정규화 완료.

---

## 🧩 6. 결과 요약

| 단계 | 작업                  | 결과            |
| -- | ------------------- | ------------- |
| 1  | IntelliJ Git 초기화    | 커밋 꼬임 해소      |
| 2  | `.git` 디렉터리 삭제      | 충돌 해결         |
| 3  | `safe.directory` 등록 | 권한 오류 해결      |
| 4  | 브랜치 재설정 및 푸시        | GitHub 정상 업로드 |
| 5  | 모듈 재설정              | src 인식 복구     |
| 6  | Copilot 설치          | 코드 어시스턴트 활성화  |

---

## ✨ 7. 오늘의 교훈

> **“IDE에서 꼬이면 GUI보다 터미널이 정답이다.”**

* IntelliJ의 Git 플러그인만 믿지 말고,
  `.git` 폴더 상태와 CLI 명령으로 직접 점검할 것.
* `.idea` 설정이 꼬여도 `src` 루트만 정확히 지정하면 다시 살아난다.
* GitHub와의 연결은 **“remote add origin → push -u”** 한 줄로 끝난다.
* Copilot은 플러그인 설치 후 재로그인하면 자동으로 연동된다.

```
