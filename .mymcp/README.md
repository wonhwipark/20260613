# AI MCP Package v1.4

이 패키지는 Roo Code와 Claude Code에서 Confluence, Jira, Perforce MCP를 쉽게 설정하기 위한 배포용 ZIP 패키지입니다.

## 사용 대상

- Roo Code 사용자
- Claude Code 사용자
- Confluence / Jira / Perforce MCP를 Local MCP 방식으로 설정하려는 사용자

## 설치 요약

1. GitHub Release에서 `ai-mcp-package-v1.4.zip`을 다운로드합니다.
2. ZIP을 압축 해제하면 `.mymcp` 폴더가 나옵니다.
3. `.mymcp` 폴더를 `C:\Users\<본인ID>\.mymcp` 위치로 둡니다.
4. 사용하는 도구에 따라 아래 파일 내용을 그대로 복사해서 입력합니다.

Roo Code:

```text
docs/02_roo_setup_prompt.md
```

Claude Code:

```text
docs/03_claude_setup_prompt.md
```

## 포함 파일

```text
.mymcp
├─ docs
│  ├─ 01_install_guide.md
│  ├─ 02_roo_setup_prompt.md
│  ├─ 03_claude_setup_prompt.md
│  ├─ 04_test_prompt.md
│  ├─ 05_troubleshooting.md
│  └─ 06_upgrade_guide.md
├─ examples
│  ├─ confluence_weekly_report.md
│  ├─ confluence_table_upload.md
│  ├─ confluence_child_page_collect.md
│  ├─ jira_examples.md
│  └─ perforce_examples.md
├─ .env.template
├─ mcp-packages.template.md
└─ README.md
```

## 중요 원칙

- `.env` 파일은 개인 인증 정보이므로 공유하지 않습니다.
- GitHub에는 `.env.template`만 포함합니다.
- 기존 Roo Code / Claude Code MCP 설정은 백업 후 병합합니다.
- Roo Code 설정 파일과 Claude Code 설정 파일은 별도로 관리합니다.
- MCP package 이름 또는 실행 경로가 확정되지 않은 경우 임의로 추측하지 않고 사용자에게 확인합니다.
- `.env` 수정 후에는 Roo Code / Claude Code 또는 MCP 서버 재시작이 필요할 수 있습니다.

## MCP package 이름 설정

`mcp-packages.template.md`에서 Confluence / Jira / Perforce MCP package 이름 또는 실행 경로를 확인해 채웁니다.

현재 패키지는 Local MCP `stdio, npx` 방식을 기준으로 하며, MCP 서버 실행 파일은 포함하지 않습니다.
