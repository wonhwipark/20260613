# Roo Code + Claude Code 공통 MCP ZIP 배포 Workflow v1.4

---

## 0. 문서 목적 및 v1.4 통합 방향

본 문서는 `Roo Code + Claude Code 공통 MCP ZIP 배포 Workflow v1.3`과 `Roo Code + Claude Code 공통 MCP ZIP 배포 Workflow v1.3_2nd.md`를 취합하여, 실제 현장에 배포 및 설치를 바로 사용할 수 있도록 정리한 최종 통합본이다.

v1.4의 기준은 다음과 같다.

- 기존 v1.3의 목적, 배경, Local MCP 구조, GitHub Release ZIP 배포 방향을 유지한다.
- v1.3_2nd.md의 Markdown 구조와 상세 프롬프트 구성을 기준으로 한다.
- Roo Code와 Claude Code는 같은 MCP 패키지와 같은 `.env`를 참조할 수 있지만, 설정 파일은 각각 별로 관리한다.
- `.env`는 공통 사용하되, 수정 필요시 Roo Code / Claude Code 또는 MCP 서버 재시작이 필요함을 내포시키도록 한다.
- 기존 설정 파일을 덮어쓰기 전에 반드시 백업한다.
- 기존 MCP 설정에 다른 서버가 있을 경우 삭제하지 않고 유지한다.
- 동일한 MCP 서버 이름이 이미 있을 경우 중복 생성하지 않고 기존 항목을 업데이트한다.
- 실제 `npx package 이름`, `사내 npm package 이름`, 또는 `ZIP 내부 실행 경로`가 파악되어 있지 않으면 AI가 임의로 추측하지 않고 사용자에게 물어보도록 한다.
- Summary의 `One Setup Prompt` 형식을 설정 구조에 맞지 않으므로 `Tool-specific Setup Prompts`로 수정한다.

---

## 1. Background

### 1.1 목적

본 문서는 현장에서 Roo Code 또는 Claude Code 환경에서 MCP(Model Context Protocol)를 쉽게 설치하고 사용할 수 있도록 하기 위한 실증 Workflow를 정의한다.

GitHub 저장소에서 목적적 공유는 아니다.

주요 목적은 다음과 같다.

- 설치용 ZIP 패키지 배포
- 설치 가이드 제공
- Roo Code 설정 프롬프트 제공
- Claude Code 설정 프롬프트 제공
- Confluence 사용 예제 제공
- Jira 사용 예제 제공
- Perforce 사용 예제 제공
- 신규 인원의 빠른 환경 구축 지원
- 현장 내 MCP 환경 구성 방식 테스트
- 사내 운영 중인 개인 인증 정보 관리 방식 테스트

각 사용자는 GitHub Release에서 ZIP 파일을 다운로드한 다음 개인 PC에 설치하여 사용한다.

---

### 1.2 설치 층 대상

본 패키지는 MCP 서버 개발자를 위한 패키지가 아니다.

목표는 다음과 같다.

- 터미널 명령 사용 최소한
- Node.js를 잘 모르더라도 설치 가능
- Roo Code 또는 Claude Code가 설치를 단계별로 안내
- 신규 인원은 짧은 시간 내에 설치 가능
- GitHub Release ZIP 다운로드만으로 설치 가능
- 개인별 인증 정보는 각자 관리
- 공통 설치 경로는 단순하게 유지
- Roo Code와 Claude Code의 설정 충돌 방지
- 기존 사용자의 MCP 설정을 훼손하지 않음

대부분의 작업을 AI가 안내한다.

다만 환경 및 권한에 따라 사용자의 승인 또는 수동의 터미널 명령 실행이 필요할 수 있다.

---

## 2. MCP와 HTTP REST

### 2.1 MCP란?

MCP(Model Context Protocol)는 AI와 외부 시스템을 연결하기 위한 표준 인터페이스이다.

구조는 다음과 같이 이해할 수 있다.

```text
AI
 ↓
MCP
 ↓
External System
```

MCP를 사용하면 AI가 다음과 같은 작업을 수행할 수 있다.

- Confluence 페이지 검색
- Confluence 표 업로드
- Confluence 하위 페이지 조회
- Jira 이슈 조회
- Jira 담당자 기준 이슈 검색
- Perforce changelist 조회
- Perforce 파일 검색
- 사내 REST API 호출
- 사내 시스템의 데이터를 AI 작업 흐름에 연결

---

### 2.2 MCP 내부는 HTTP REST API를 사용할 수 있다

대부분의 MCP 서버는 내부적으로 HTTP REST API를 호출한다.

```text
AI
 ↓
MCP Server
 ↓
HTTP REST API
 ↓
Confluence / Jira / Perforce / Internal System
```

즉, 사용자는 MCP를 사용하고, MCP 내부에서는 HTTP REST API가 사용될 수 있다.

사용자는 Confluence REST API, Jira REST API, Perforce 명령이 또는 사내 API의 세부 사용법을 매번 찾아야 할 필요가 없다.

AI가 MCP 서버를 통해 외부 시스템과 통신한다.

---

### 2.3 본 Workflow에서 사용하는 방식

본 Workflow는 Local MCP 방식을 사용한다.

실제 HTTP MCP Server는 사용하지 않는다.

```text
Roo Code ───┐
            ├─── Local MCP Server ─── HTTP REST API ─── Confluence
Claude Code ┘    (stdio, npx)                           Jira
                                                        Perforce
```

핵심은 다음과 같다.

- Roo Code와 Claude Code는 각각 MCP client 역할을 한다.
- MCP 서버는 사용자 PC에서 Local MCP Server로 실행된다.
- 실행 방식은 기본적으로 `stdio` 기반이다.
- 실행 명령은 일반적으로 `npx` 또는 `node`가 될 수 있다.
- MCP 서버 내부에서 Confluence / Jira / Perforce / 사내 API를 호출한다.
- 인증 정보는 사용자 PC의 `.env` 파일에서 읽는다.
- 실제 서버를 이용하지 않으므로 운영 부담이 낮다.
- 개인 PC에서 실행되므로 개인 인증 정보 관리가 명확하다.

---

## 3. 권장 구조

### 3.1 전체 위치

MCP 패키지의 `.env` 인증 정보는 Roo Code와 Claude Code가 공통으로 사용할 수 있다.

하지만 Roo Code 설정 파일과 Claude Code 설정 파일은 서로 다르므로 각각 별도로 관리한다.

```text
              Roo Code
                  ↓
           (Roo 설정 파일)
                  ↓
                  ↓
            ─────────────────
            │ MCP Package │ ─── .env (공통 인증 정보)
            │(stdio, npx) │
            ──────────┬────────
                   ↓
           HTTP REST API
                   ↓
    Confluence / Jira / Perforce


             Claude Code
                  ↓
        (Claude 설정 파일)
                  ↓
                  ↓
            ─────────────────
            │ MCP Package │ ─── .env (공통 인증 정보)
            │(stdio, npx) │
            ──────────┬────────
                   ↓
           HTTP REST API
                   ↓
    Confluence / Jira / Perforce
```

---

### 3.2 공통 관리 대상

다음 항목은 Roo Code와 Claude Code가 공통으로 사용할 수 있다.

```text
C:\Users\<ID>\.mymcp
```

공통 설치 경로에 포함되는 항목은 다음과 같다.

- `docs`
- `examples`
- `.env.template`
- `.env`
- `README.md`
- MCP package 실행 정보
- MCP package 이름 또는 실행 경로 템플릿
- 설치 가이드
- 테스트 프롬프트

---

### 3.3 개별 관리 대상

다음 항목은 Roo Code와 Claude Code가 각각 별도로 관리해야 한다.

- Roo Code MCP 설정 파일
- Claude Code MCP 설정 파일
- Roo Code 내부 MCP 등록 상태
- Claude Code 내부 MCP 등록 상태
- 각 도구의 재시작 상태
- 각 도구의 project/global 설정 범위

---

### 3.4 경로 용어 정리

본 문서에서 사용하는 경로는 크게 두 종류이다.

| 구분 | 의미 | 예 |
|---|---|---|
| 패키지 설치 경로 | ZIP을 압축 해제하는 공통 MCP 폴더 | `C:\Users\<ID>\.mymcp` |
| 도구 설정 경로 | Roo Code 또는 Claude Code가 MCP 설정을 읽는 설정 파일 위치 | `mcp_settings.json`, `.mcp.json` 등 |

`C:\Users\<ID>\.mymcp`는 패키지 설치 경로이다.

Roo Code 또는 Claude Code의 MCP 설정 파일 위치는 별도로 확인해야 한다.

따라서 설정 프롬프트에서는 다음 위치를 사용한다.

```text
Roo Code 또는 Claude Code가 MCP 설정을 읽는 위치를 찾아서,
기존 설정을 백업하고,
새 MCP 설정을 추가해준다.
```

---

## 4. GitHub Repository 구조

### 4.1 기본 Repository 구조

GitHub Repository는 공동 개발 저장소가 아니라 배포 저장소이다.

권장 구조는 다음과 같다.

```text
GitHub Repository
│
├─ Release
│     └─ ai-mcp-package-v1.0.zip
│
├─ docs
│     ├─ 01_install_guide.md
│     ├─ 02_roo_setup_prompt.md
│     ├─ 03_claude_setup_prompt.md
│     ├─ 04_test_prompt.md
│     ├─ 05_troubleshooting.md
│     └─ 06_upgrade_guide.md
│
├─ examples
│     ├─ confluence_weekly_report.md
│     ├─ confluence_table_upload.md
│     ├─ confluence_child_page_collect.md
│     ├─ jira_examples.md
│     └─ perforce_examples.md
│
├─ .env.template
├─ mcp-packages.template.md
└─ README.md
```

---

### 4.2 ZIP 내부 구조

사용자가 Release ZIP을 다운로드하고 압축 해제하면 다음 구조가 나와야 한다.

```text
.mymcp
│
├─ docs
│     ├─ 01_install_guide.md
│     ├─ 02_roo_setup_prompt.md
│     ├─ 03_claude_setup_prompt.md
│     ├─ 04_test_prompt.md
│     ├─ 05_troubleshooting.md
│     └─ 06_upgrade_guide.md
│
├─ examples
│     ├─ confluence_weekly_report.md
│     ├─ confluence_table_upload.md
│     ├─ confluence_child_page_collect.md
│     ├─ jira_examples.md
│     └─ perforce_examples.md
│
├─ .env.template
├─ mcp-packages.template.md
└─ README.md
```

---

### 4.3 npx 내부 package 방식

현재 Workflow는 기본적으로 Local MCP `stdio, npx` 방식을 권장한다.

이 방식에서는 ZIP 내부에 MCP 서버 실행 파일이 직접 포함되지 않을 수 있다.

이 경우 실제 MCP 서버는 `npx`를 통해 실행된다.

예는 다음과 같다.

```json
{
  "mcpServers": {
    "confluence": {
      "command": "npx",
      "args": [
        "-y",
        "<CONFLUENCE_MCP_PACKAGE_NAME>"
      ]
    }
  }
}
```

여기서 `<CONFLUENCE_MCP_PACKAGE_NAME>` 자리에 실제 package 이름이 들어가야 한다.

---

### 4.4 ZIP 내부 node 실행 방식

만약 MCP 서버 실행 파일을 ZIP 내부에 포함한다면 `npx package 이름` 대신 실제 실행 경로를 사용한다.

예는 다음과 같다.

```json
{
  "mcpServers": {
    "confluence": {
      "command": "node",
      "args": [
        "C:\\Users\\<ID>\\.mymcp\\servers\\confluence\\server.js"
      ]
    }
  }
}
```

이 방식을 사용할 경우 ZIP 구조는 다음처럼 확장될 수 있다.

```text
.mymcp
│
├─ servers
│     ├─ confluence
│     │     └─ server.js
│     ├─ jira
│     │     └─ server.js
│     └─ perforce
│           └─ server.js
│
├─ docs
├─ examples
├─ .env.template
├─ mcp-packages.template.md
└─ README.md
```

---

### 4.5 본 문서의 기본 판단

초기 배포 단계에서는 실제 package 이름이 파악되지 않을 수 있다.

따라서 v1.4에서는 다음 위치를 사용한다.

```text
문서에는 package 이름 입력 절차를 두고,
파악된 사내 package 이름이 나오면 나중에 mcp-packages.template.md에 기본값으로 추가한다.
```

즉, Roo Code 또는 Claude Code가 설치 중에 진행되면서 MCP 서버 실행 방식과 package 이름을 사용자에게 물어볼 수 있도록 한다.

---

## 5. MCP package 이름 또는 실행 경로

### 5.1 왜 필요한가?

문서에서 `npm 패키지를 설치한다`, `npx로 MCP Package를 실행한다`고 설명하더라도 실제 MCP 서버 이름이 없으면 MCP 설정을 작성할 수 없다.

예를 들어 다음 설정을 부분적으로 본다.

```json
{
  "mcpServers": {
    "confluence": {
      "command": "npx",
      "args": [
        "-y",
        "???"
      ]
    }
  }
}
```

`???` 부분에 실제 package 이름이 필요하다.

---

### 5.2 가능한 실행 방식

MCP 서버 실행 방식은 다음 중 하나가 될 수 있다.

| 방식 | 설명 | 실제 필요한 값 |
|---|---|---|
| 공개 npm package 실행 | 공개 npm registry에 있는 MCP package를 npx로 실행 | npm package 이름 |
| 사내 npm registry package 실행 | 사내 npm registry에 등록된 package를 npx로 실행 | 사내 package 이름, registry 인증 정보 |
| ZIP 내부 node 실행 | ZIP 내부에 포함된 server.js 등을 node로 실행 | 실행 파일 경로 |
| 이미 설치된 MCP 서버 실행 | PC에 이미 설치된 명령이 또는 실행 파일 사용 | command 이름 또는 절대 경로 |

---

### 5.3 문서에 package 이름을 명시할 수 없는 경우

사내 표준 MCP package 이름이 파악되어 있지 않으면 `mcp-packages.template.md`에 다음처럼 명시한다.

```text
# MCP Package Names

## Confluence
Package:
@company/confluence-mcp-server

## Jira
Package:
@company/jira-mcp-server

## Perforce
Package:
@company/perforce-mcp-server

## 실행 방식
- npx 방식
- 사내 npm registry 사용
- registry 인증 필요 사부 확인 필요
```

---

### 5.4 package 이름이 파악되지 않는 경우

package 이름이 파악되지 않았으면 Roo Code 또는 Claude Code가 설치 중 사용자에게 물어보도록 한다.

프롬프트에는 다음 위치를 넣는다.

```text
MCP 서버 실행 방식과 package 이름이 명확하지 않으면 임의로 추측하지 말고 사용자에게 물어봐준다.
```

사용자에게 확인할 항목은 다음과 같다.

- Confluence MCP package 이름 또는 실행 경로
- Jira MCP package 이름 또는 실행 경로
- Perforce MCP package 이름 또는 실행 경로
- 사내 npm registry 사용 여부
- npm registry 인증이 필요한지 여부
- ZIP 내부 MCP 서버 포함 여부
- 이미 PC에 설치된 MCP 서버 사용 여부

---

## 6. 사용자 설치 Workflow

### Step 1. ZIP 다운로드

사용자는 GitHub Release에서 ZIP 파일을 다운로드한다.

예:

```text
ai-mcp-package-v1.0.zip
```

---

### Step 2. 압축 해제

사용자는 ZIP 파일을 다음 경로에 압축 해제한다.

```text
C:\Users\<ID>\.mymcp
```

예:

```text
C:\Users\wh1112.park\.mymcp
```

압축 해제 후 구조는 다음과 같아야 한다.

```text
.mymcp
│
├─ docs
├─ examples
├─ .env.template
├─ mcp-packages.template.md
└─ README.md
```

---

### Step 3. Roo Code 또는 Claude Code 실행

사용자는 앞으로 이용할 도구를 실행한다.

- Roo Code를 사용할 경우 `docs/02_roo_setup_prompt.md` 내용을 복사하여 Roo Code에 입력한다.
- Claude Code를 사용할 경우 `docs/03_claude_setup_prompt.md` 내용을 복사하여 Claude Code에 입력한다.

---

### Step 4. AI가 설치를 안내

AI는 다음 순서로 설치를 안내한다.

1. 현재 OS 확인
2. 공통 설치 경로 확인
3. Roo Code 또는 Claude Code가 MCP 설정을 읽는 위치 확인
4. 기존 MCP 설정 파일 존재 사부 확인
5. 기존 설정 파일 백업
6. Node.js 설치 사부 확인
7. npm 사용 가능 사부 확인
8. MCP 서버 실행 방식 확인
9. package 이름 또는 실행 경로 확인
10. `.env.template` 확인
11. 기존 `.env` 존재 사부 확인
12. `.env` 작성 또는 누락 항목 보완
13. MCP 설정 작성 또는 기존 설정 병합
14. 중복 서버명 처리
15. 재시작 필요 사부 안내
16. Confluence 연결 테스트
17. Jira 연결 테스트
18. Perforce 연결 테스트

---

### Step 5. 설치 완료 확인

설치 완료 후 사용자는 다음을 확인한다.

- Roo Code 또는 Claude Code에서 MCP 서버 목록이 보이는지
- Confluence 검색이 되는지
- Jira 조회가 되는지
- Perforce changelist 조회가 되는지
- `.env` 인증 정보가 제대로 사용되는지
- 기존 MCP 설정이 삭제되지 않았는지
- Roo Code 설정과 Claude Code 설정이 서로 떨어져 있는지

---

## 7. .env.template

### 7.1 기본 템플릿

`.env.template` 파일은 다음 내용을 포함한다.

```text
# Confluence
CONFLUENCE_URL=
CONFLUENCE_USERNAME=
CONFLUENCE_TOKEN=

# Jira
JIRA_URL=
JIRA_USERNAME=
JIRA_TOKEN=

# Perforce
P4PORT=
P4USER=
P4PASSWD=
```

---

### 7.2 보안 위치

보안 위치는 다음과 같다.

- `.env` 파일은 GitHub에 업로드하지 않는다.
- `.env.template`만 배포한다.
- Token 및 Password는 각인별 관리한다.
- Token 및 Password는 README나 안내 문서에 직접 작성하지 않는다.
- `.env`는 사용자 PC에만 존재해야 한다.
- `.env` 공유는 금지된다.
- 화면 캡처, 로그, 이메일 등록 시 인증 정보가 노출되지 않도록 한다.

---

### 7.3 Roo Code와 Claude Code와 .env 공통 사용

`.env`는 다음 경로 하나를 공통으로 사용할 수 있다.

```text
C:\Users\<ID>\.mymcp\.env
```

공통 사용 위치는 다음과 같다.

```text
.env는 Roo Code와 Claude Code가 공통으로 참조한다.
단, 각 도구의 MCP 설정 파일은 별도로 관리한다.
.env 수정 후에는 Roo Code/Claude Code를 재시작하거나 MCP 서버를 재시작한다.
```

---

### 7.4 기존 .env가 있는 경우

기존 `.env` 파일이 있으면 덮어씌우지 않는다.

AI는 다음 방식으로 처리해야 한다.

1. 기존 `.env` 파일 존재 사부 확인
2. 기존 `.env` 파일 백업 사부 확인
3. 누락된 항목만 확인
4. 사용자에게 누락 항목만 질문
5. 기존 값 유지
6. 새 값이 필요한 경우 사용자 승인 후 수정
7. 수정 후 Roo Code / Claude Code / MCP 서버 재시작 필요 여부 안내

백업 파일명은 다음과 같다.

```text
.env.bak_YYYYMMDD_HHMMSS
```

---

## 8. mcp-packages.template.md

### 8.1 파일 목적

`mcp-packages.template.md`는 실제 MCP 서버 실행 방식과 package 이름을 정리하기 위한 템플릿이다.

초기에는 package 이름이 파악되지 않으므로 placeholder를 둔다.

---

### 8.2 템플릿 내용

```text
# MCP Package Names

이 파일은 Roo Code / Claude Code에서 MCP 서버를 등록할 때 사용할 package 이름 또는 실행 경로를 정리하기 위한 템플릿이다.

아직 사내 표준 package 이름이 파악되지 않았으면 아래 값을 비우고, Roo Code 또는 Claude Code 설정 프롬프트 진행 중 사용자에게 물어보도록 한다.

---

## 1. Confluence MCP

실행 방식:
- [ ] npx 공개 npm package
- [ ] npx 사내 npm registry package
- [ ] ZIP 내부 node 실행 파일
- [ ] 이미 PC에 설치된 MCP 서버 실행
- [ ] 아직 미파악

Package 이름 또는 실행 경로:

```text
<CONFLUENCE_MCP_PACKAGE_NAME_OR_EXECUTION_PATH>
```

예:

```text
@company/confluence-mcp-server
```

또는:

```text
C:\Users\<ID>\.mymcp\servers\confluence\server.js
```

---

## 2. Jira MCP

실행 방식:
- [ ] npx 공개 npm package
- [ ] npx 사내 npm registry package
- [ ] ZIP 내부 node 실행 파일
- [ ] 이미 PC에 설치된 MCP 서버 실행
- [ ] 아직 미파악

Package 이름 또는 실행 경로:

```text
<JIRA_MCP_PACKAGE_NAME_OR_EXECUTION_PATH>
```

예:

```text
@company/jira-mcp-server
```

또는:

```text
C:\Users\<ID>\.mymcp\servers\jira\server.js
```

---

## 3. Perforce MCP

실행 방식:
- [ ] npx 공개 npm package
- [ ] npx 사내 npm registry package
- [ ] ZIP 내부 node 실행 파일
- [ ] 이미 PC에 설치된 MCP 서버 실행
- [ ] 아직 미파악

Package 이름 또는 실행 경로:

```text
<PERFORCE_MCP_PACKAGE_NAME_OR_EXECUTION_PATH>
```

예:

```text
@company/perforce-mcp-server
```

또는:

```text
C:\Users\<ID>\.mymcp\servers\perforce\server.js
```

---

## 4. 사내 npm registry 사용 여부

사내 npm registry 사용 여부:

```text
<YES_OR_NO>
```

사내 npm registry URL:

```text
<INTERNAL_NPM_REGISTRY_URL>
```

인증 필요 여부:

```text
<YES_OR_NO>
```

인증 방식:

```text
<NPM_TOKEN_OR_LOGIN_OR_OTHER>
```

---

## 5. 공통 원칙

- package 이름을 모르면 임의로 추측하지 않는다.
- 실행 경로를 모르면 임의로 만들지 않는다.
- 실제 설정 후 사용자에게 확인한다.
- Roo Code와 Claude Code는 같은 package 이름을 사용할 수 있다.
- 단, Roo Code 설정 파일과 Claude Code 설정 파일은 각각 별로 관리한다.
```

---

## 9. Roo Code 설정 프롬프트

### 9.1 파일 위치

Roo Code 설정 프롬프트는 다음 파일에 저장한다.

```text
docs/02_roo_setup_prompt.md
```

---

### 9.2 사용자 안내 문구

사용자는 `docs/02_roo_setup_prompt.md` 파일 내용을 그대로 복사하여 Roo Code에 입력한다.

---

### 9.3 Roo Code 설정 프롬프트 본문

아래 내용은 `docs/02_roo_setup_prompt.md`에 포함된다.

```text
내 PC의 MCP 패키지를 아래 경로에 설치했어.

C:\Users\<ID>\.mymcp

이 경로의 .env.template 파일을 기준으로 Roo Code에서 사용할 MCP 설정을 해줘.

설정 위치:
- C:\Users\<ID>\.mymcp 는 MCP 패키지와 .env를 보관하는 공통 설치 경로이다.
- Roo Code가 MCP 설정을 읽는 위치는 별도로 확인해야 한다.
- Roo Code 설정 파일은 Claude Code 설정 파일과 분리해서 관리한다.
- .env 파일은 Roo Code와 Claude Code가 공통으로 참조할 수 있다.
- 기존 설정을 덮어쓰기 전에 반드시 백업한다.
- 기존 Roo Code MCP 설정에 다른 서버가 있으면 삭제하지 말고 유지한다.
- 동일한 이름의 MCP 서버가 이미 있으면 중복 생성하지 말고 기존 항목을 업데이트한다.
- Claude Code 설정 파일은 수정하지 않는다.
- MCP 서버 실행 방식과 package 이름이 명확하지 않으면 임의로 추측하지 말고 사용자에게 물어봐준다.
- package 이름을 모르면 자동 package 이름을 만들지 않는다.
- .env 수정 필요시 Roo Code 또는 MCP 서버 재시작이 필요함을 안내한다.

요구사항:
1. 현재 PC에서 Roo Code가 MCP 설정을 읽는 파일 위치를 확인해줘.
   - 실제 파일 위치를 찾을 수 없으면 해당 경로를 알려줘.
   - 설정 파일이 여러 개 있으면 project 설정인지 global 설정인지 구분해줘.
   - 사용자가 명확히 지시하지 않았다면 일반적으로 상위 global/user 설정을 우선 검토해줘.

2. 기존 Roo Code MCP 설정 파일이 있으면 수정 전 백업해줘.
   - 백업 파일명 예: mcp_settings.json.bak_YYYYMMDD_HHMMSS
   - 기존 설정 파일의 다른 MCP 서버 항목은 삭제하지 말.
   - 기존 설정 파일이 손상되어 있으면 바로 수정하지 말고 사용자에게 알려줘.

3. 설정 파일이 없으면 Roo Code가 인식할 수 있는 위치에 새로 작성해줘.
   - 새로 작성하는 경우 작성 위치를 알려줘.
   - 새 설정 파일에는 이번에 추가하는 MCP 서버 항목만 포함해도 된다.
   - 다만 기존 설정 파일이 다른 위치에 있으면 중복 설정이 되지 않도록 확인해줘.

4. Node.js가 설치되어 있는지 확인하고, 없으면 설치 방법을 안내해줘.
   - node 명령이 되는지 확인해줘.
   - npm 명령이 되는지 확인해줘.
   - npx 명령이 되는지 확인해줘.

5. 필요한 npm 패키지가 설치 완료되어 있으면 설치 방법을 안내하거나 가능할 경우 설치해줘.
   - 단, package 이름이 파악되지 않았으면 설치하지 말.
   - package 이름이 파악된 다음 설치 또는 npx 실행 설정을 진행해줘.

6. MCP 서버 실행 방식과 package 이름을 확인해줘.
   가능한 방식은 다음과 같다.
   - npx로 공개 npm package 실행
   - npx로 사내 npm registry package 실행
   - ZIP 내부에 포함된 node 실행 파일 실행
   - 이미 PC에 설치된 MCP 서버 실행

7. docs 또는 mcp-packages.template.md 파일에서 Confluence / Jira / Perforce MCP package 이름 또는 실행 경로를 찾아줘.
   - Confluence MCP package 이름 또는 실행 경로를 확인해줘.
   - Jira MCP package 이름 또는 실행 경로를 확인해줘.
   - Perforce MCP package 이름 또는 실행 경로를 확인해줘.

8. package 이름 또는 실행 경로가 파악되지 않으면 임의로 추측하지 말고 사용자에게 아래 항목을 하나씩 물어봐줘.
   - Confluence MCP package 이름 또는 실행 경로
   - Jira MCP package 이름 또는 실행 경로
   - Perforce MCP package 이름 또는 실행 경로
   - 사내 npm registry 사용 여부
   - npm registry 인증이 필요한지 여부
   - ZIP 내부 MCP 서버를 사용할 지 여부
   - 이미 PC에 설치된 MCP 서버를 사용할 지 여부

9. package 이름을 모르면 설정을 중단하고 사용자 확인을 요청해줘.
   - 임의 package 이름을 작성하지 말.
   - 인터넷 검색으로 임의 대체 package를 고르지 말.
   - 사내 package 이름은 사용자가 제공한 값 또는 문서에 명시된 값만 사용해줘.

10. .env.template을 기반으로 .env 파일을 작성해줘.
   - 기존 .env 파일이 있으면 덮어씌우지 말고 누락 항목만 확인해줘.
   - 기존 .env 파일이 있으면 추출시 .env.bak_YYYYMMDD_HHMMSS 형식으로 백업해줘.
   - 각 항목을 하나씩 물어봐줘.
     - Confluence URL
     - Confluence Username
     - Confluence Token
     - Jira URL
     - Jira Username
     - Jira Token
     - P4PORT
     - P4USER
     - P4PASSWD
   - 입력받은 값으로 .env 파일을 작성하거나 보완해줘.

11. .env는 아래 경로를 사용하도록 설정해줘.

   C:\Users\<ID>\.mymcp\.env

12. 파악된 MCP package 이름 또는 실행 경로를 사용해서 Roo Code MCP 설정을 작성해줘.
   - Roo Code에서 적용 가능한 mcp_settings.json 형식으로 작성해줘.
   - command와 args를 명확히 작성해줘.
   - npx 방식이면 command는 npx를 사용해줘.
   - ZIP 내부 node 실행 방식이면 command는 node를 사용하고 args에 server.js 경로를 넣어줘.
   - .env 파일을 사용할 수 있도록 env 또는 envFile 설정 방식을 적용해줘.
   - Roo Code에서 envFile을 직접 지정하지 않으면 필요한 환경 변수를 env 항목에 매핑하는 방식을 설계해줘.

13. Confluence MCP를 등록해줘.

14. Jira MCP를 등록해줘.

15. Perforce MCP를 등록해줘.

16. 기존 Roo Code MCP 설정에 다른 서버가 있으면 삭제하지 말고 유지해줘.

17. 동일한 이름의 MCP 서버가 이미 있으면 중복 생성하지 말고 기존 항목을 업데이트해줘.
   - 예: confluence, jira, perforce 이름이 이미 있으면 기존 항목을 온전히 교체
   - 다른 이름의 서버는 건드리지 않음

18. Claude Code 설정 파일은 수정하지 말.

19. 설정 완료 후 Roo Code 또는 MCP 서버 재시작이 필요한지 알려줘.
   - .env를 새로 만들었거나 수정했으면 재시작 필요성을 안내해줘.
   - MCP 서버가 이미 실행 중이면 재시작해야 새 인증 정보가 반영될 수 없음을 안내해줘.

20. 설정 완료 후 Confluence 검색 테스트를 실행해줘.
   - 테스트가 실패하면 인증 문제인지, package 실행 문제인지, 네트워크 문제인지, 권한 문제인지 구분해줘.
   - 실패 로그에 token/password가 노출되지 않도록 주의해줘.

21. 가능하면 Jira 테스트도 실행해줘.

22. 가능하면 Perforce 테스트도 실행해줘.

23. 최종 결과를 아래 형식으로 요약해줘.
   - Roo Code MCP 설정 파일 위치
   - 백업 파일 위치
   - 사용한 .env 파일 위치
   - Confluence MCP 실행 방식
   - Jira MCP 실행 방식
   - Perforce MCP 실행 방식
   - 재시작 필요 여부
   - 테스트 결과
```

---

## 10. Claude Code 설정 프롬프트

### 10.1 파일 위치

Claude Code 설정 프롬프트는 다음 파일에 저장한다.

```text
docs/03_claude_setup_prompt.md
```

---

### 10.2 사용자 안내 문구

사용자는 `docs/03_claude_setup_prompt.md` 파일 내용을 그대로 복사하여 Claude Code에 입력한다.

---

### 10.3 Claude Code 설정 프롬프트 본문

아래 내용은 `docs/03_claude_setup_prompt.md`에 포함된다.

```text
내 PC의 MCP 패키지를 아래 경로에 설치했어.

C:\Users\<ID>\.mymcp

이 경로의 .env.template 파일을 기준으로 Claude Code에서 사용할 MCP 설정을 해줘.

설정 위치:
- C:\Users\<ID>\.mymcp 는 MCP 패키지와 .env를 보관하는 공통 설치 경로이다.
- Claude Code가 MCP 설정을 읽는 위치 또는 설정 방식은 별도로 확인해야 한다.
- Claude Code 설정 파일은 Roo Code 설정 파일과 분리해서 관리한다.
- .env 파일은 Roo Code와 Claude Code가 공통으로 참조할 수 있다.
- 기존 설정을 덮어쓰기 전에 반드시 백업한다.
- 기존 Claude Code MCP 설정에 다른 서버가 있으면 삭제하지 말고 유지한다.
- 동일한 이름의 MCP 서버가 이미 있으면 중복 생성하지 말고 기존 항목을 업데이트한다.
- Roo Code 설정 파일은 수정하지 않는다.
- MCP 서버 실행 방식과 package 이름이 명확하지 않으면 임의로 추측하지 말고 사용자에게 물어봐준다.
- package 이름을 모르면 자동 package 이름을 만들지 않는다.
- .env 수정 필요시 Claude Code 또는 MCP 서버 재시작이 필요함을 안내한다.

요구사항:
1. 현재 PC에서 Claude Code가 MCP 설정을 읽는 파일 위치 또는 설정 방식을 확인해줘.
   - .mcp.json 방식인지 확인해줘.
   - claude mcp add 명령이 방식인지 확인해줘.
   - 프로젝트 단위 설정인지 확인해줘.
   - 사용자 전역 설정인지 확인해줘.
   - 설정 파일 위치를 찾을 수 없으면 해당 경로를 알려줘.

2. 기존 Claude Code MCP 설정 파일이 있으면 수정 전 백업해줘.
   - 백업 파일명 예: .mcp.json.bak_YYYYMMDD_HHMMSS
   - 기존 설정 파일의 다른 MCP 서버 항목은 삭제하지 말.
   - 기존 설정 파일이 손상되어 있으면 바로 수정하지 말고 사용자에게 알려줘.

3. 설정 파일이 없으면 Claude Code가 인식할 수 있는 위치에 새로 작성해줘.
   - 새로 작성하는 경우 작성 위치를 알려줘.
   - Claude Code가 명령이 기반 등록을 권장하는 경우, .mcp.json 직접 수정 방식과 claude mcp add 방식 중 더 적합한 방식을 설명해줘.

4. Node.js가 설치되어 있는지 확인하고, 없으면 설치 방법을 안내해줘.
   - node 명령이 되는지 확인해줘.
   - npm 명령이 되는지 확인해줘.
   - npx 명령이 되는지 확인해줘.

5. 필요한 npm 패키지가 설치 완료되어 있으면 설치 방법을 안내하거나 가능할 경우 설치해줘.
   - 단, package 이름이 파악되지 않았으면 설치하지 말.
   - package 이름이 파악된 다음 설치 또는 npx 실행 설정을 진행해줘.

6. MCP 서버 실행 방식과 package 이름을 확인해줘.
   가능한 방식은 다음과 같다.
   - npx로 공개 npm package 실행
   - npx로 사내 npm registry package 실행
   - ZIP 내부에 포함된 node 실행 파일 실행
   - 이미 PC에 설치된 MCP 서버 실행

7. docs 또는 mcp-packages.template.md 파일에서 Confluence / Jira / Perforce MCP package 이름 또는 실행 경로를 찾아줘.
   - Confluence MCP package 이름 또는 실행 경로를 확인해줘.
   - Jira MCP package 이름 또는 실행 경로를 확인해줘.
   - Perforce MCP package 이름 또는 실행 경로를 확인해줘.

8. package 이름 또는 실행 경로가 파악되지 않으면 임의로 추측하지 말고 사용자에게 아래 항목을 하나씩 물어봐줘.
   - Confluence MCP package 이름 또는 실행 경로
   - Jira MCP package 이름 또는 실행 경로
   - Perforce MCP package 이름 또는 실행 경로
   - 사내 npm registry 사용 여부
   - npm registry 인증이 필요한지 여부
   - ZIP 내부 MCP 서버를 사용할 지 여부
   - 이미 PC에 설치된 MCP 서버를 사용할 지 여부

9. package 이름을 모르면 설정을 중단하고 사용자 확인을 요청해줘.
   - 임의 package 이름을 작성하지 말.
   - 인터넷 검색으로 임의 대체 package를 고르지 말.
   - 사내 package 이름은 사용자가 제공한 값 또는 문서에 명시된 값만 사용해줘.

10. .env.template을 기반으로 .env 파일을 작성해줘.
   - 기존 .env 파일이 있으면 덮어씌우지 말고 누락 항목만 확인해줘.
   - 기존 .env 파일이 있으면 추출시 .env.bak_YYYYMMDD_HHMMSS 형식으로 백업해줘.
   - 각 항목을 하나씩 물어봐줘.
     - Confluence URL
     - Confluence Username
     - Confluence Token
     - Jira URL
     - Jira Username
     - Jira Token
     - P4PORT
     - P4USER
     - P4PASSWD
   - 입력받은 값으로 .env 파일을 작성하거나 보완해줘.

11. .env는 아래 경로를 사용하도록 설정해줘.

   C:\Users\<ID>\.mymcp\.env

12. 파악된 MCP package 이름 또는 실행 경로를 사용해서 Claude Code MCP 설정을 작성해줘.
   - Claude Code에서 적용 가능한 .mcp.json 또는 claude mcp add 명령이 형식으로 작성해줘.
   - command와 args를 명확히 작성해줘.
   - npx 방식이면 command는 npx를 사용해줘.
   - ZIP 내부 node 실행 방식이면 command는 node를 사용하고 args에 server.js 경로를 넣어줘.
   - .env 파일을 사용할 수 있도록 env 또는 envFile 설정 방식을 적용해줘.
   - Claude Code에서 envFile을 직접 지정하지 않으면 필요한 환경 변수를 env 항목에 매핑하는 방식을 설계해줘.

13. Confluence MCP를 등록해줘.

14. Jira MCP를 등록해줘.

15. Perforce MCP를 등록해줘.

16. 기존 Claude Code MCP 설정에 다른 서버가 있으면 삭제하지 말고 유지해줘.

17. 동일한 이름의 MCP 서버가 이미 있으면 중복 생성하지 말고 기존 항목을 업데이트해줘.
   - 예: confluence, jira, perforce 이름이 이미 있으면 기존 항목을 온전히 교체
   - 다른 이름의 서버는 건드리지 않음

18. Roo Code 설정 파일은 수정하지 말.

19. 설정 완료 후 Claude Code 또는 MCP 서버 재시작이 필요한지 알려줘.
   - .env를 새로 만들었거나 수정했으면 재시작 필요성을 안내해줘.
   - MCP 서버가 이미 실행 중이면 재시작해야 새 인증 정보가 반영될 수 없음을 안내해줘.

20. 설정 완료 후 Confluence 검색 테스트를 실행해줘.
   - 테스트가 실패하면 인증 문제인지, package 실행 문제인지, 네트워크 문제인지, 권한 문제인지 구분해줘.
   - 실패 로그에 token/password가 노출되지 않도록 주의해줘.

21. 가능하면 Jira 테스트도 실행해줘.

22. 가능하면 Perforce 테스트도 실행해줘.

23. 최종 결과를 아래 형식으로 요약해줘.
   - Claude Code MCP 설정 파일 위치 또는 등록 방식
   - 백업 파일 위치
   - 사용한 .env 파일 위치
   - Confluence MCP 실행 방식
   - Jira MCP 실행 방식
   - Perforce MCP 실행 방식
   - 재시작 필요 여부
   - 테스트 결과
```

---

## 11. 연결 테스트 프롬프트

### 11.1 파일 위치

연결 테스트 프롬프트는 다음 파일에 저장한다.

```text
docs/04_test_prompt.md
```

---

### 11.2 Confluence 읽기 테스트

```text
Confluence에서 내가 접근 가능한 페이지 하나를 검색해줘.

조건:
- 인증 정보가 정확한지 확인해줘.
- 검색 결과가 없으면 권한 문제인지, 검색이 문제인지, 연결 문제인지 구분해줘.
- 실패 로그에 token/password가 노출되지 않도록 해줘.
```

---

### 11.3 Confluence 테이블 업로드 테스트

```text
Confluence의 테스트 페이지를 만들거나, 내가 지정한 테스트 페이지에 아래 표를 추가해줘.

| Name | Status |
|------|--------|
| Test1 | OK |
| Test2 | PASS |

조건:
- 실제 업무 페이지에는 업로드하지 말.
- 테스트 페이지 또는 사용자가 명시한 페이지에만 업로드해줘.
- 업로드 전에 대상 페이지 제목과 URL을 확인해줘.
- 업로드 후 페이지 링크를 알려줘.
```

---

### 11.4 Confluence 하위 페이지 취합 테스트

```text
아래 Confluence main 페이지의 하위 페이지를 검색해서 개인별 주간보고로 보이는 페이지만 분류해줘.

Main Page:
<CONFLUENCE_MAIN_PAGE_URL>

조건:
- main 페이지 자체 내용만 보지 말고 child page를 반드시 확인해줘.
- 하위 페이지 목록을 먼저 보여줘.
- 하위 페이지 중 개인별 주간보고로 보이는 페이지만 옆으로 분류해줘.
- 찾지 못한 사람은 "미파악"으로 표시해줘.
- 추천 페이지 링크를 함께 정리해줘.
```

---

### 11.5 Jira 테스트

```text
Jira에서 내가 담당자인 이슈를 검색해줘.

조건:
- Open 상태 이슈와 In Progress 상태 이슈를 구분해줘.
- 이슈 Key, Summary, Status, Assignee, Updated 날짜를 표로 정리해줘.
- 조회 실패 시 인증 문제인지, JQL 문제인지, 권한 문제인지 구분해줘.
```

---

### 11.6 Perforce 테스트

```text
Perforce에서 최근 changelist 5개를 조회해줘.

조건:
- changelist 번호, 작성자, 날짜, 설명을 정리해줘.
- P4PORT, P4USER, P4PASSWD 설정이 정확한지 확인해줘.
- 조회 실패 시 인증 문제인지, 서버 연결 문제인지, workspace/client 문제인지 구분해줘.
```

---

## 12. 배포 Workflow

### 12.1 배포 담당자 역할

배포 담당자는 다음을 관리한다.

- ZIP 패키지 생성
- GitHub Release 업로드
- README 최적화
- 설치 가이드 최적화
- Roo Code 설정 프롬프트 최적화
- Claude Code 설정 프롬프트 최적화
- 테스트 프롬프트 최적화
- troubleshooting 문서 최적화
- upgrade guide 최적화
- package 이름 또는 실행 경로 최적화
- 배포 후 사용자 피드백 수집

---

### 12.2 ZIP 생성

배포 담당자는 다음 이름으로 ZIP을 생성한다.

```text
ai-mcp-package-v1.0.zip
```

버전이 올라가면 다음처럼 변경한다.

```text
ai-mcp-package-v1.1.zip
ai-mcp-package-v1.2.zip
ai-mcp-package-v1.3.zip
ai-mcp-package-v1.4.zip
```

---

### 12.3 GitHub Release 업로드

GitHub Release 설명 예는 다음과 같다.

```text
Title: ai-mcp-package v1.4

File:
ai-mcp-package-v1.4.zip

Description:
- Roo Code MCP 설치 지원
- Claude Code MCP 설치 지원
- Confluence/Jira/Perforce MCP 설정 프롬프트 포함
- Local MCP(stdio, npx) 방식 기준
- .env.template 포함
- mcp-packages.template.md 포함
- 기존 설정 백업 및 병합 방식 반영
- Roo/Claude 설정 파일 분리 관리 반영
- package 이름 미파악 시 사용자에게 확인하는 프롬프트 보강
- .env 공통 사용 및 재시작 안내 반영
```

---

### 12.4 현장에 공유할 메시지

현장에 공유할 메시지는 다음과 같이 작성한다.

```text
아래 GitHub Release에서 ZIP 파일을 다운로드한 다음,
C:\Users\<본인ID>\.mymcp 경로에 압축 해제하세요.

그 다음 사용하는 도구에 따라 아래 파일 내용을 그대로 복사해서 입력하면 됩니다.

Roo Code 사용 시:
docs/02_roo_setup_prompt.md

Claude Code 사용 시:
docs/03_claude_setup_prompt.md

AI가 Node.js 확인, package 실행 방식 확인, .env 작성, MCP 설정, 백업, 테스트까지 단계별로 안내합니다.

주의:
- .env 파일은 개인 인증 정보이므로 공유하지 마세요.
- 기존 MCP 설정이 있으면 AI가 백업한 후 병합하도록 돼 있습니다.
- package 이름 또는 실행 경로가 파악되지 않는 경우 AI가 물어볼 수 있습니다.
```

---

## 13. Release 전 체크리스트

배포 담당자는 Release 전에 다음을 확인한다.

### 13.1 파일 구성 확인

```text
[ ] docs/01_install_guide.md 존재
[ ] docs/02_roo_setup_prompt.md 존재
[ ] docs/03_claude_setup_prompt.md 존재
[ ] docs/04_test_prompt.md 존재
[ ] docs/05_troubleshooting.md 존재
[ ] docs/06_upgrade_guide.md 존재
[ ] examples/confluence_weekly_report.md 존재
[ ] examples/confluence_table_upload.md 존재
[ ] examples/confluence_child_page_collect.md 존재
[ ] examples/jira_examples.md 존재
[ ] examples/perforce_examples.md 존재
[ ] .env.template 존재
[ ] mcp-packages.template.md 존재
[ ] README.md 존재
```

---

### 13.2 보안 확인

```text
[ ] .env 파일이 ZIP에 포함되지 않았는지 확인
[ ] 실제 token/password가 문서에 포함되지 않았는지 확인
[ ] 개인 계정 정보가 examples에 포함되지 않았는지 확인
[ ] 사내 URL 공개 가능 범위 확인
[ ] GitHub Release 접근 권한 확인
[ ] README에 보안 주의사항 포함
```

---

### 13.3 설치 검증

```text
[ ] 깨끗한 PC 또는 새로운 테스트 폴더에서 ZIP 압축 해제 확인
[ ] C:\Users\<ID>\.mymcp 구조 확인
[ ] Roo Code 설정 프롬프트 동작 확인
[ ] Claude Code 설정 프롬프트 동작 확인
[ ] 기존 설정 백업 동작 확인
[ ] 기존 설정 병합 동작 확인
[ ] .env 작성 동작 확인
[ ] package 이름 미파악 시 질문 동작 확인
[ ] Confluence 테스트 확인
[ ] Jira 테스트 확인
[ ] Perforce 테스트 확인
```

---

## 14. Upgrade Guide

### 14.1 기본 원칙

신버전 ZIP이 나온 개인 인증 정보인 `.env`는 보존해야 한다.

기존 Roo Code / Claude Code 설정 파일은 삭제하지 않는다.

업그레이드 대상은 주로 다음이다.

- docs
- examples
- README
- `.env.template`
- `mcp-packages.template.md`

---

### 14.2 업그레이드 절차

```text
1. 기존 C:\Users\<ID>\.mymcp 폴더를 백업한다.
   예:
   C:\Users\<ID>\.mymcp_bak_YYYYMMDD_HHMMSS

2. 신 ZIP을 새 폴더에 압축 해제한다.

3. 기존 .env 파일을 유지한다.

4. docs 폴더를 신버전으로 교체한다.

5. examples 폴더를 신버전으로 교체한다.

6. README.md를 신버전으로 교체한다.

7. .env.template을 신버전으로 교체한다.

8. mcp-packages.template.md를 신버전으로 교체한다.

9. 기존 .env에서 신로 필요한 항목이 있는지 비교한다.

10. Roo Code 또는 Claude Code를 재시작한다.

11. 연결 테스트를 다시 실행한다.
```

---

### 14.3 업그레이드 시 금지 사항

```text
- 기존 .env 파일을 임의로 삭제하지 않는다.
- 기존 Roo Code MCP 설정 파일을 임의로 삭제하지 않는다.
- 기존 Claude Code MCP 설정 파일을 임의로 삭제하지 않는다.
- 기존 설정 파일을 백업 없이 덮어씌우지 않는다.
- 기존 MCP 서버 항목을 전체 초기화하지 않는다.
- package 이름을 임의로 변경하지 않는다.
```

---

## 15. Troubleshooting

### 15.1 Node.js를 찾지 못하는 경우

증상:

```text
node 명령을 찾을 수 없음
npm 명령을 찾을 수 없음
npx 명령을 찾을 수 없음
```

확인할 항목:

```text
- Node.js 설치 여부
- PATH 환경 변수 반영 여부
- 터미널 재시작 여부
- Roo Code / Claude Code 재시작 여부
```

조치:

```text
Node.js를 설치한 다음 터미널과 Roo Code/Claude Code를 재시작한다.
```

---

### 15.2 npm package 이름을 모르는 경우

증상:

```text
MCP 설정을 만들 수 없음
npx 실행 실패
package not found 오류 발생
```

조치:

```text
mcp-packages.template.md를 확인한다.
문서에 package 이름이 없으면 관리자 또는 배포 담당자에게 확인한다.
AI가 임의 package 이름을 만들지 않도록 한다.
```

---

### 15.3 사내 npm registry 인증 실패

증상:

```text
npm install 실패
npx 실행 실패
401 Unauthorized
403 Forbidden
```

확인할 항목:

```text
- 사내 npm registry URL
- npm login 여부
- npm token 설정 여부
- VPN 또는 사내망 연결 여부
- proxy 설정 여부
```

---

### 15.4 Confluence 인증 실패

증상:

```text
Confluence 검색 실패
401 Unauthorized
403 Forbidden
```

확인할 항목:

```text
- CONFLUENCE_URL
- CONFLUENCE_USERNAME
- CONFLUENCE_TOKEN
- token 만료 여부
- 페이지 접근 권한
- 사내망/VPN 연결 여부
```

---

### 15.5 Jira 인증 실패

증상:

```text
Jira 이슈 조회 실패
401 Unauthorized
403 Forbidden
```

확인할 항목:

```text
- JIRA_URL
- JIRA_USERNAME
- JIRA_TOKEN
- token 만료 여부
- Jira 프로젝트 접근 권한
```

---

### 15.6 Perforce 인증 실패

증상:

```text
Perforce changelist 조회 실패
login failed
P4PORT connection failed
```

확인할 항목:

```text
- P4PORT
- P4USER
- P4PASSWD
- Perforce ticket 만료 여부
- 사내망 연결 여부
- p4 client 설정 여부
```

---

### 15.7 .env 수정 후 반영되지 않는 경우

원인:

```text
MCP 서버가 이미 실행 중이면 기존 환경 변수를 계속 사용할 수 있다.
```

조치:

```text
Roo Code를 재시작한다.
Claude Code를 재시작한다.
필요하면 MCP 서버 프로세스를 종료한 다음 다시 실행한다.
```

---

### 15.8 기존 MCP 설정이 사라진 경우

원인:

```text
설정 파일을 병합하지 않고 덮어씌운 경우
백업 없이 설정을 교체한 경우
```

조치:

```text
백업 파일을 확인한다.
mcp_settings.json.bak_YYYYMMDD_HHMMSS 또는 .mcp.json.bak_YYYYMMDD_HHMMSS에서 기존 설정을 복구한다.
```

---

## 16. README.md 권장 내용

`README.md`에는 다음 내용을 포함한다.

```text
# AI MCP Package

이 패키지는 Roo Code와 Claude Code에서 Confluence, Jira, Perforce MCP를 쉽게 설정하기 위한 배포용 ZIP 패키지이다.

## 사용 대상

- Roo Code 사용자
- Claude Code 사용자
- Confluence/Jira/Perforce를 AI에서 사용하려는 현장 인원
- 신규 환경 구축이 필요한 인원

## 설치 요약

1. GitHub Release에서 ZIP 다운로드
2. C:\Users\<ID>\.mymcp 경로에 압축 해제
3. 사용하는 도구에 맞는 설정 프롬프트 실행

Roo Code:
docs/02_roo_setup_prompt.md

Claude Code:
docs/03_claude_setup_prompt.md

## 주의사항

- .env 파일은 개인 인증 정보이므로 공유하지 않는다.
- 기존 MCP 설정을 백업한 후 병합한다.
- Roo Code 설정과 Claude Code 설정은 별도로 관리한다.
- package 이름이 파악되지 않는 경우 AI가 사용자에게 물어볼 수 있다.

## 테스트

설치 후 docs/04_test_prompt.md를 사용하여 Confluence/Jira/Perforce 연결을 확인한다.
```

---

## 17. 핵심 원칙

최종 핵심 원칙은 다음과 같다.

- GitHub는 배포용이다.
- ZIP 파일은 GitHub Release에 업로드한다.
- 사용자는 Release ZIP만 다운로드한다.
- Local MCP(stdio, npx)를 사용한다.
- 실제 HTTP MCP Server는 사용하지 않는다.
- MCP 내부는 HTTP REST API를 사용할 수 있다.
- MCP package는 Roo Code와 Claude Code가 공통으로 사용할 수 있다.
- `.env`는 공통으로 사용할 수 있다.
- Roo Code 설정 파일은 Roo Code 전용으로 관리한다.
- Claude Code 설정 파일은 Claude Code 전용으로 관리한다.
- 기존 설정은 반드시 백업한 후 병합한다.
- 기존 설정의 다른 MCP 서버가 있으면 삭제하지 않는다.
- 동일 서버명이 있으면 중복 생성하지 않고 업데이트한다.
- `.env` 수정 후에는 Roo Code / Claude Code 또는 MCP 서버 재시작이 필요하다.
- 실제 package 이름 또는 실행 경로가 불명확하면 AI가 임의로 추측하지 않고 사용자에게 물어본다.
- 대부분의 작업을 AI가 안내한다.
- 사용자는 필요한 인증 정보와 package 이름을 제공한다.
- 보안 정보는 GitHub에 업로드하지 않는다.

---

## 18. 최종 Summary

기존 `One Setup Prompt` 형식 Roo Code와 Claude Code 프롬프트가 분리되어 있는 설정 구조에 맞지 않는다.

따라서 최종 Summary는 다음과 같이 사용한다.

```text
One ZIP
+
Tool-specific Setup Prompts
  - Roo Code Setup Prompt
  - Claude Code Setup Prompt
+
Personal Installation
        ↓
Roo Code + Claude Code 공통 MCP 사용
        ↓
Local MCP + HTTP REST 구조
        ↓
.env 공통 사용
        ↓
Roo/Claude 설정 파일 개별 관리
        ↓
기존 설정 백업 + 병합
        ↓
완성 보수 최적화
```

---

## 19. v1.4에서 반영된 변경 사항

### 19.1 C 항목 반영

기존 형식:

```text
먼저 현재 OS와 Roo Code/Claude Code 설정 경로를 확인해줘.
```

최종 반영 형식:

```text
Roo Code 또는 Claude Code가 MCP 설정을 읽는 위치를 찾아서,
기존 설정을 백업하고,
새 MCP 설정을 추가해줘.
```

Roo Code 프롬프트와 Claude Code 프롬프트에 각각 반영된다.

---

### 19.2 D 항목 반영

`.env` 공통 사용 충돌 방지 방식이 Roo Code 프롬프트와 Claude Code 프롬프트에 각각 반영된다.

최종 위치는 다음과 같다.

```text
.env는 공통
Roo 설정은 Roo만
Claude 설정은 Claude만
기존 설정은 백업 후 병합
.env 수정 후 재시작 안내
```

---

### 19.3 B 항목 반영

실제 `npx package 이름`, `사내 npm package 이름`, 또는 `ZIP 내부 실행 경로`가 파악되지 않았을 때 Roo Code / Claude Code가 설치 중 사용자에게 물어보도록 반영된다.

최종 위치는 다음과 같다.

```text
MCP 서버 실행 방식과 package 이름이 명확하지 않으면 임의로 추측하지 말고 사용자에게 물어봐준다.
```

Roo Code 프롬프트와 Claude Code 프롬프트 모두에 다음 확인 항목이 포함된다.

- Confluence MCP package 이름 또는 실행 경로
- Jira MCP package 이름 또는 실행 경로
- Perforce MCP package 이름 또는 실행 경로
- 사내 npm registry 사용 여부
- npm registry 인증 필요 여부
- ZIP 내부 MCP 서버 포함 여부
- 이미 PC에 설치된 MCP 서버 사용 여부

---

## 20. 최종 결과물 목록

v1.4 기준 최종 ZIP에는 다음 파일을 포함하는 것이 권장된다.

```text
.mymcp
│
├─ docs
│     ├─ 01_install_guide.md
│     ├─ 02_roo_setup_prompt.md
│     ├─ 03_claude_setup_prompt.md
│     ├─ 04_test_prompt.md
│     ├─ 05_troubleshooting.md
│     └─ 06_upgrade_guide.md
│
├─ examples
│     ├─ confluence_weekly_report.md
│     ├─ confluence_table_upload.md
│     ├─ confluence_child_page_collect.md
│     ├─ jira_examples.md
│     └─ perforce_examples.md
│
├─ .env.template
├─ mcp-packages.template.md
└─ README.md
```

선택적으로 MCP 서버 실행 파일을 ZIP에 포함할 수 있다.

```text
.mymcp
│
├─ servers
│     ├─ confluence
│     │     └─ server.js
│     ├─ jira
│     │     └─ server.js
│     └─ perforce
│           └─ server.js
```

다만, 이 경우 `mcp-packages.template.md`에는 npx package 이름 대신 node 실행 경로를 명시해야 한다.
