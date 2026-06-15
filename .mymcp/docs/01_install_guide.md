# AI MCP Package 설치 가이드

이 문서는 Roo Code와 Claude Code에서 공통 MCP 패키지를 설치하고 설정하기 위한 가이드입니다.
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

