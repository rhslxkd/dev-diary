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

---
## 📅 2025-11-05 — 🚀 React 구축 + Spring Boot 연동 (CORS 설정 및 REST API 테스트 포함)
## 🧩 1️⃣ IntelliJ 기본 설정

### 🌱 새 프로젝트 생성
- **File → New → Project → Spring Initializr**
- **Dependencies 추가 (Spring Boot 백엔드 핵심 세트)**  

| Dependency | 역할 설명 |
|-------------|------------|
| **Lombok** | 반복되는 코드(Setter, Getter, Constructor 등) 자동 생성 |
| **Spring Web** | REST API 및 MVC 웹 애플리케이션 구축 |
| **Spring Security** | 인증(Authentication)과 인가(Authorization) 기능 제공 |
| **JDBC API** | 자바와 DB를 연결하는 기본 API |
| **Spring Data JPA** | ORM(Object Relational Mapping) 기반의 CRUD 자동화 |
| **Oracle Driver** | 오라클 DB 연결용 드라이버 |
| **Validation** | DTO, 폼 데이터 유효성 검증 |
| **CycloneDX SBOM support** | 프로젝트 의존성 보안 추적 도구 |

📘 **정리:**  
Spring Boot는 Web + JPA + Oracle이 핵심 축이며,  
Security와 Validation은 안전한 데이터 처리를 위한 필수 구성요소다.

---

## ⚙️ 2️⃣ Node.js 설치 및 React 환경 구축

### 🧭 Node.js 설치
1. 공식 사이트: [https://nodejs.org/ko/download](https://nodejs.org/ko/download)  
2. 사용하는 OS(Windows, macOS 등)에 맞는 설치 프로그램 실행  
3. 설치 후 IntelliJ 터미널 또는 PowerShell에서 버전 확인:
   ```bash
   node -v
   npm -v
명령어	역할
node -v	Node.js 런타임 버전 확인 (JS 실행 엔진 확인용)
npm -v	Node Package Manager 버전 확인 (패키지 관리 툴)

📂 React 프로젝트 생성
터미널에서 원하는 위치로 이동 (예: cd src/main)

다음 명령어로 React 앱 생성:

bash
코드 복사
npx create-react-app frontend
→ frontend 폴더가 생기고 내부 구조 자동 생성됨.

이후 경로 이동:

bash
코드 복사
cd frontend
📦 React Router & Hook Form 설치
bash
코드 복사
npm install react-router-dom
npm install react-hook-form
패키지명	역할
react-router-dom	페이지 간 이동(라우팅)을 담당하는 라이브러리
react-hook-form	입력 폼 데이터를 간단하게 관리할 수 있는 훅(Hook) 기반 폼 라이브러리

📘 예시:

jsx
코드 복사
// react-hook-form 예제
import { useForm } from "react-hook-form";

function MyForm() {
  const { register, handleSubmit } = useForm();
  const onSubmit = data => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register("name")} placeholder="이름 입력" />
      <input {...register("email")} placeholder="이메일 입력" />
      <button type="submit">전송</button>
    </form>
  );
}
🧠 3️⃣ 실행 방법
프로젝트 실행 경로 이동:

bash
코드 복사
cd src/main/frontend
React 개발 서버 실행:

bash
코드 복사
npm start
→ 브라우저가 자동으로 열리고 http://localhost:3000에서 확인 가능.

백엔드(Spring Boot)는 http://localhost:8080에서 작동 중이어야 하며,
React ↔ Spring 통신을 위해 CORS 설정 또는 Proxy 설정을 반드시 추가해야 함.

⚙️ 4️⃣ Spring Boot ↔ React CORS 설정
React 개발 서버(3000번 포트)에서 Spring Boot API(8080 포트)로 요청을 보낼 때,
브라우저 보안 정책(Same-Origin Policy) 때문에 기본적으로 막힌다.
이때 Spring의 CORS 설정으로 포트 간 통신을 허용해야 한다.

✅ WebConfig.java 예시
java
코드 복사
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**")                         // /api로 시작하는 모든 경로 허용
                .allowedOrigins("http://localhost:3000")        // React 개발 서버 주소
                .allowedMethods("GET", "POST", "PUT", "DELETE") // 허용할 메서드
                .allowedHeaders("*")                            // 모든 헤더 허용
                .allowCredentials(true)                         // 인증 정보(쿠키, 세션) 허용
                .maxAge(3600);                                  // preflight 캐시 1시간 유지
    }
}
⚙️ 작동 원리
React에서 fetch("http://localhost:8080/api/member/main") 요청을 보낼 때,
브라우저가 먼저 OPTIONS (Preflight) 요청을 보낸다.

위 설정 덕분에 Spring이 “이건 3000번에서 온 요청이니 괜찮아.” 하고 허락함.

그 다음 실제 GET/POST 요청이 정상적으로 수행되고 JSON 응답을 받는다.

🧠 참고
Spring Security를 사용하는 경우엔 Security 설정에도 CORS를 허용해야 한다:

java
코드 복사
@Bean
SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
    http
        .csrf(csrf -> csrf.disable())
        .cors(cors -> {})  // WebConfig의 CORS 설정 적용
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/api/**").permitAll()
            .anyRequest().permitAll()
        );
    return http.build();
}
🧪 5️⃣ REST API Test 방법
프론트엔드와 백엔드가 완전히 분리된 구조에서는
백엔드가 정상적으로 응답하는지 미리 테스트하는 게 중요하다.
이를 위해 REST Client나 ARC (Advanced REST Client) 같은 도구를 사용한다.

✅ 방법 1. Visual Studio Code REST Client
VSCode에서 REST Client 확장팩을 설치한다.

.http 또는 .rest 파일을 만들어 아래처럼 작성:

http
코드 복사
### GET 요청 예시
GET http://localhost:8080/api/member/main

### POST 요청 예시
POST http://localhost:8080/api/member
Content-Type: application/json

{
  "name": "홍길동",
  "email": "test@example.com"
}
Send Request 버튼을 눌러 바로 결과를 확인할 수 있다.
GET, POST, PUT, DELETE 요청 모두 지원.

✅ 방법 2. ARC (Advanced REST Client)
다운로드: https://github.com/advanced-rest-client/arc-electron/releases
(출처 표기하면 사용 가능. 오픈소스 클라이언트 프로그램)

설치 후, 다음과 같은 REST 테스트 수행 가능:

GET: 데이터 조회 (/api/member/main)

POST: 새 데이터 추가

PUT: 데이터 수정

DELETE: 데이터 삭제

추가 기능: 로그인, 로그아웃, 세션 토큰 테스트 등

📌 REST 테스트의 목적

상황	의미
❌ 테스트 실패	백엔드(Spring Boot) 쪽 오류 가능성 99%
✅ 테스트 성공	프론트엔드(React) 연결 로직 오류 가능성 99%

→ 즉, REST Client로 백엔드를 독립적으로 검증함으로써
React 문제인지, API 문제인지 빠르게 분리할 수 있다.

⚠️ 6️⃣ DB 연결 주의사항
application.properties 혹은 application.yml에 정확한 DB 정보 입력 필수:

properties
코드 복사
spring.datasource.url=jdbc:oracle:thin:@localhost:1521:FREE
spring.datasource.username=guro
spring.datasource.password=guro1234
spring.datasource.driver-class-name=oracle.jdbc.OracleDriver

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
⚠️ 오타나 포트, SID 오류가 있으면 Spring 서버가 실행되지 않는다.
JDBC URL, username, password를 꼭 확인할 것.

✍️ 첨언 (by Moro)
React는 프론트엔드(View), Spring Boot는 백엔드(API) 역할을 수행한다.
두 영역은 fetch()를 통해 JSON 데이터를 주고받는다.

폴더 구조를 명확히 분리해야 유지보수가 쉽다.
(backend / frontend 경로를 구분)

커밋 시 .gitignore에 **node_modules/**는 반드시 제외할 것.
(용량이 크고 npm install로 언제든 재생성 가능)

addCorsMappings()는 React(3000) 과 Spring Boot(8080) 간 통신을 허용하는 핵심 브릿지다.
없으면 CORS 에러로 JSON 통신이 차단된다.

REST Client / ARC를 활용하면 백엔드를 프론트와 독립적으로 검증할 수 있다.

✅ 요약

Spring Boot 기본 세팅 및 Dependencies 구성

Node.js / npm 설치 및 React 환경 구성

react-router-dom / react-hook-form 설치  

CORS 설정으로 3000 ↔ 8080 포트 연결 허용

REST API 테스트 도구(REST Client, ARC) 활용

DB 연결 설정 및 서버 실행 확인

이제 Spring Boot (백엔드) ↔ React (프론트엔드) 연동이 완료되고,
REST 테스트 도구를 통해 API 안정성까지 확인할 수 있다.

---
## 📘 2025.11.17 — OGV Project: Python Agent 개발 기록

## 🧩 1. Python Agent 디렉터리 생성

OGV(Spring Boot + React) 프로젝트 내부에 `python-agent/` 디렉터리를 생성함.

**목표**
- Google Gemini + ADK 기반 Agent 서버 구축  
- React 프론트엔드와 통신하는 독립 서버 구조  
- 향후 벡터 DB + 뉴스 자동화 파이프라인 기반 마련  


---

## 🧪 2. 가상환경(venv) 생성 및 활성화

### ✔ 왜 필요한가?
- 패키지 충돌 방지  
- 프로젝트별 독립 환경 제공  
- OS 차이 제거  
- “환경은 venv, 코드는 GitHub”로 역할 분리  

### ▶ 생성
```bash
python -m venv venv
▶ 활성화
bash
코드 복사
venv\Scripts\activate
📦 3. 필수 패키지 설치
bash
코드 복사
pip install fastapi uvicorn[standard] google-genai google-adk
패키지 설명

FastAPI: Python 경량 API 서버

Uvicorn: FastAPI 실행기 (Spring의 Tomcat 역할)

google-genai: Gemini 공식 Python SDK

google-adk: Agent Development Kit (멀티 에이전트 구성)

🔐 4. Gemini API Key 환경변수 설정
✔ 왜 코드에 Key를 쓰면 안 되는가?
GitHub에 올라가는 순간 즉시 유출

제3자가 마음대로 API 호출

요금 폭탄 + 악용 위험 발생

Key는 항상 OS 환경변수에만 저장

▶ Windows 설정
bash
코드 복사
setx GOOGLE_API_KEY "YOUR_API_KEY"
VSCode 재시작 필요.

🧠 5. FastAPI + ADK 서버(app.py) 작성
python
코드 복사
from fastapi import FastAPI
from pydantic import BaseModel
from google.adk.agents import Agent
from google.adk.runners import InMemoryRunner
from google.adk.tools import google_search

import os

api_key = os.getenv("GOOGLE_API_KEY")
if not api_key:
    raise RuntimeError("환경변수 GOOGLE_API_KEY 필요")

os.environ["GOOGLE_API_KEY"] = api_key
os.environ["GOOGLE_GENAI_USE_VERTEXAI"] = "FALSE"

# ADK Agent 구성
root_agent = Agent(
    name="news_short_agent",
    model="gemini-2.5-flash-lite",
    description="뉴스 기반 요약 및 정보 제공",
    instruction="한국어로 짧고 명확하게 답변.",
    tools=[google_search],
)

runner = InMemoryRunner(agent=root_agent)

app = FastAPI()

class ChatRequest(BaseModel):
    message: str

class ChatResponse(BaseModel):
    reply: str

@app.post("/chat", response_model=ChatResponse)
async def chat(req: ChatRequest):
    result = await runner.run_debug(req.message)
    reply = getattr(result, "output_text", str(result))
    return ChatResponse(reply=reply)
▶ 6. 서버 실행
bash
코드 복사
python -m uvicorn app:app --reload --port 8000
API 문서 확인:
http://localhost:8000/docs

⚠️ 7. venv를 GitHub에 올리면 안 되는 이유
OS별 실행파일 포함 → 다른 컴퓨터에서 실행 불가

용량 수백 MB 증가 → 레포 무거워짐

캐시/경로/민감 정보 포함

팀 환경 충돌

Python 공식 문서에서도 업로드 금지

✔ GitHub에 올려야 하는 것
app.py

requirements.txt

README.md

.gitignore

📄 8. .gitignore 설정
gitignore
코드 복사
# Python venv
venv/
**/venv/

# Cache files
__pycache__/
**/__pycache__/
*.pyc
이미 Git 추적 중이라면:

bash
코드 복사
git rm -r --cached venv
💾 9. Git Commit & Push
bash
코드 복사
git add .
git commit -m "Add python-agent with FastAPI + ADK setup and ignore venv"
git push origin main
🎯 10. 오늘 전체 작업 흐름 요약
python-agent 디렉터리 생성

가상환경 생성 및 활성화

FastAPI + Uvicorn + GenAI + ADK 설치

환경변수로 Gemini API Key 등록

ADK Agent + FastAPI 서버(app.py) 구현

/chat API 정상 동작 확인

.gitignore로 venv 제거

GitHub에 안전하게 commit & push 완료

yaml
코드 복사

---

.gitignore로 venv 제거

안전하게 GitHub에 commit & push 완료
