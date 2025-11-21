# 📘 OGV5 GitHub 프로젝트 초기 세팅 기록 (2025.11.21)

> 실무형 GitHub 모노레포 프로젝트를 처음 셋업할 때의 모든 과정 정리.
> Spring Boot + React + FastAPI 기반의 3-Service 구조.

---

## 🧱 1. GitHub 저장소 생성

* **Repository Name:** `OGV5`
* **Description:** "OGV5 Multi-Service Architecture"
* **공개 여부:** 🔓 **Public Repository**

```bash
# 기본 생성 구조
OGV5/
 └─ README.md
```

**브랜치 전략:**

* 기본 브랜치: `main`
* 작업 브랜치: `dev`

```bash
cd /e/study/Projects
git clone https://github.com/rhslxkd/OGV5.git
cd OGV5
git checkout -b dev
```

---

## 📂 2. 기본 디렉토리 구조 생성

```bash
mkdir backend frontend python-agent docs
ls -a
```

```bash
OGV5/
 ├─ backend/
 ├─ frontend/
 ├─ python-agent/
 ├─ docs/
 └─ README.md
```

```bash
git add .
git commit -m "chore: initialize backend/frontend/python-agent/docs directories"
git push origin dev
```

---

## ☕ 3. Spring Boot Backend (IntelliJ)

* **Java 17 / Gradle**
* Dependencies: Web, JPA, Security, Lombok, Validation

프로젝트 생성 후 `backend/backend` 문제 → 직접 폴더 이동 및 정리

```bash
# 위치 확인
OGV5/backend/build.gradle
OGV5/backend/src/...
```

```bash
git add .
git commit -m "feat: initialize Spring Boot backend project"
git push origin dev
```

---

## ⚛️ 4. React Frontend (Vite)

```bash
cd frontend
npm create vite@latest
# → 프로젝트명: frontend
npm install
```

```bash
git add .
git commit -m "feat: initialize React frontend"
git push origin dev
```

---

## 🐍 5. Python Agent (FastAPI)

```bash
cd python-agent
python -m venv .venv
.venv\Scripts\activate
pip install fastapi uvicorn
```

`main.py` 예시:

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/movie")
def movie_check():
    return {\        "status": "ok",
        "service": "python-agent"
    }
```

```bash
uvicorn main:app --reload
# → http://127.0.0.1:8000/movie → 200 OK
```

```bash
git add .
git commit -m "feat: add python-agent FastAPI skeleton"
git push origin dev
```

---

## 🚫 6. .gitignore 설정 (서비스별 분리)

### 📍 루트 OGV5/.gitignore

```gitignore
.DS_Store
Thumbs.db
node_modules/
__pycache__/
*.pyc
.venv/
*.log
.idea/
.iml
.vscode/
.gradle/
build/
.env
.env.*
```

### 📍 backend/.gitignore (Spring Boot)

```gitignore
HELP.md
.gradle
build/
!gradle/wrapper/gradle-wrapper.jar
.idea
*.iml
out/
.apt_generated
.classpath
.project
.settings
bin/
.DS_Store
Thumbs.db
```

### 📍 frontend/.gitignore (React/Vite)

> Vite 템플릿에서 자동 생성된 ignore 파일 그대로 사용

### 📍 python-agent/.gitignore

```gitignore
.venv/
__pycache__/
*.pyc
.env
.env.*
.vscode/
```

```bash
git add .
git commit -m "feat: add .ignore to OGV5/backend/frontend/python-agent"
git push origin dev
```

---

## 🏗️ 7. 전체 폴더 구조 정리

```
OGV5/
 ├─ backend/
 │    ├─ src/
 │    ├─ build.gradle
 │    └─ .gitignore

 ├─ frontend/
 │    ├─ src/
 │    ├─ package.json
 │    └─ .gitignore

 ├─ python-agent/
 │    ├─ main.py
 │    └─ .gitignore

 ├─ docs/
 ├─ README.md
 ├─ .gitignore
 └─ .git/
```

---

## 🚀 8. dev → main PR 생성

GitHub에서 Pull Request 생성:

* **base:** `main`
* **compare:** `dev`

```md
PR 제목: chore: initialize OGV5 multi-service architecture

PR 내용:
- backend(Spring Boot) 초기화
- frontend(React/Vite) 초기화
- python-agent(FastAPI) 초기화
- 서비스별 .gitignore 분리
- 루트 .gitignore 정리
- 프로젝트 뼈대 구조 완성
```

→ Merge Pull Request 실행

---

## 🔄 9. 로컬 main 최신화

```bash
git checkout main
git pull origin main
git checkout dev
```

앞으로의 모든 작업은 `dev` 브랜치에서 진행 후, `main`으로 PR.

---

## ✅ 오늘 성과 요약

* GitHub Repository 생성 및 브랜치 전략 정립
* Spring Boot / React / FastAPI 각 서비스 초기화 완료
* 실무 수준의 모노레포 구조 확립
* 서비스별 .gitignore 구성 정리
* FastAPI 200 OK 테스트 성공
* dev → main PR 생성 및 병합 완료
