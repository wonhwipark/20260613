# AI MCP Package - Roo Code & Claude Code 공통 MCP 배포

Roo Code와 Claude Code에서 Confluence, Jira, Perforce MCP(Model Context Protocol)를 쉽게 설정하고 사용하기 위한 배포 및 설정 가이드 저장소입니다.

## 📋 문서 구성

### 1. **claude_code_mcp_settings_extract_prompt.md**
- Claude Code에서 현재 사용 중인 MCP 설정을 확인하는 프롬프트
- 기존 MCP 설정을 Workflow에 반영하기 위한 작업 지시서
- 인증 정보 마스킹 및 보안 검증 포함

### 2. **Roo_Code_Claude_Code_공통_MCP_ZIP_배포_Workflow_v1.4.md**
최종 통합 배포 Workflow로, 다음 내용을 포함합니다:

#### 핵심 구성
- **MCP 개요**: MCP와 HTTP REST API의 관계 설명
- **권장 구조**: Roo Code와 Claude Code의 공통/분리 관리 방식
- **GitHub 저장소 구조**: Release ZIP 배포 방식
- **설치 Workflow**: 사용자 단계별 설치 프로세스

#### 설정 가이드
- **Roo Code 설정 프롬프트** (Step 9)
- **Claude Code 설정 프롬프트** (Step 10)
- **연결 테스트 프롬프트** (Step 11)
- **Troubleshooting 가이드** (Step 15)
- **Upgrade 가이드** (Step 14)

#### 배포 및 관리
- **배포 Workflow** (Step 12)
- **Release 체크리스트** (Step 13)
- **.env.template 관리** (Step 7)
- **mcp-packages.template.md 관리** (Step 8)

## 🎯 주요 특징

### 1. **Local MCP 기반 구조**
- stdio 방식의 Local MCP Server 사용
- npx 또는 node 기반 실행
- 실제 HTTP MCP Server 불필요

### 2. **공통/분리 관리**
- `.env`: Roo Code와 Claude Code가 공통 참조
- 설정 파일: 각 도구별 독립적 관리
- 기존 설정: 백업 후 병합

### 3. **사용자 친화적 설정**
- AI가 단계별로 설치 안내
- 인증 정보 개인별 관리
- package 이름 미파악 시 사용자 확인

### 4. **보안 중심**
- 인증 정보는 GitHub에 업로드하지 않음
- .env.template 기반 개인 설정
- 기존 설정 자동 백업

## 📦 배포 구조

```
.mymcp/
├── docs/
│   ├── 01_install_guide.md
│   ├── 02_roo_setup_prompt.md
│   ├── 03_claude_setup_prompt.md
│   ├── 04_test_prompt.md
│   ├── 05_troubleshooting.md
│   └── 06_upgrade_guide.md
├── examples/
│   ├── confluence_weekly_report.md
│   ├── confluence_table_upload.md
│   ├── confluence_child_page_collect.md
│   ├── jira_examples.md
│   └── perforce_examples.md
├── .env.template
├── mcp-packages.template.md
└── README.md
```

## 🚀 빠른 시작

### 설치 단계

1. **GitHub Release에서 ZIP 다운로드**
   ```
   ai-mcp-package-v1.4.zip
   ```

2. **압축 해제**
   ```
   C:\Users\<ID>\.mymcp
   ```

3. **설정 프롬프트 실행**
   - Roo Code 사용: `docs/02_roo_setup_prompt.md` 복사 후 입력
   - Claude Code 사용: `docs/03_claude_setup_prompt.md` 복사 후 입력

4. **AI가 자동으로 설치 진행**
   - Node.js 확인
   - MCP 설정 작성
   - .env 파일 생성
   - 연결 테스트

## ⚙️ 지원 MCP

- **Confluence**: 페이지 검색, 테이블 업로드, 하위 페이지 수집
- **Jira**: 이슈 검색, 담당자 기준 조회
- **Perforce**: Changelist 조회, 파일 검색

## 📝 중요 원칙

### 보안
- ❌ `.env` 파일은 GitHub에 업로드하지 않음
- ✅ `.env.template`만 배포 (사용자가 개인 설정 작성)
- ❌ Token, Password, Secret 문서에 노출 금지

### 설정 관리
- ✅ `.env`: 공통 사용
- ✅ Roo Code 설정: Roo Code 전용
- ✅ Claude Code 설정: Claude Code 전용
- ✅ 기존 설정은 반드시 백업 후 병합

### 동작 원칙
- ✅ AI가 대부분의 작업 안내
- ✅ Package 이름 미파악 시 사용자에게 물어보기
- ✅ 기존 MCP 설정 삭제 금지
- ✅ .env 수정 후 Roo Code/Claude Code 재시작 필요

## 🔧 Troubleshooting

주요 오류 및 해결 방법:

| 증상 | 원인 | 해결책 |
|------|------|--------|
| node 명령 찾을 수 없음 | Node.js 미설치 | Node.js 설치 후 재시작 |
| Package not found | npx 실행 실패 | mcp-packages.template.md 확인 |
| 401 Unauthorized | 인증 정보 오류 | .env 확인 및 Token 유효성 검증 |
| .env 반영 안 됨 | MCP 서버 재시작 필요 | Roo/Claude Code 또는 MCP 서버 재시작 |

자세한 내용은 `Roo_Code_Claude_Code_공통_MCP_ZIP_배포_Workflow_v1.4.md`의 Step 15 Troubleshooting을 참조하세요.

## 📚 추가 리소스

- **설치 가이드**: docs/01_install_guide.md
- **사용 예제**: examples/ 폴더
- **Upgrade 가이드**: docs/06_upgrade_guide.md

## 📧 문의

이 저장소의 문서 및 Workflow에 대한 질문이 있으신 경우 연락주세요.

---

**v1.4 업데이트 (2026-06-13)**
- Roo Code와 Claude Code 설정 프롬프트 분리
- .env 공통 사용 및 재시작 안내 강화
- Package 이름 미파악 시 사용자 확인 프로세스 추가
- 기존 설정 백업 및 병합 방식 통합
