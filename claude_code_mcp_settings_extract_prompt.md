# Claude Code MCP 설정 확인 및 Workflow 반영 프롬프트

## 목의 

현재 PC의 Claude Code에서 설정으로 사용 중인 MCP 설정을 확인하고, 현재 적용 중인 Confluence / Jira / Perforce MCP 설정의 실행 방식과 package 이름 또는 실행 경로를 Workflow 문서에 반영하기 위한 프롬프트이다.

이 문서는 `Roo Code + Claude Code 공통 MCP ZIP 배포 Workflow v1.4`를 기준으로, 실제 현재 환경에서 실행 중인 Claude Code MCP 설정을 source of truth로 사용하기 위한 작업 지시서이다.

---

## Claude Code에 입력할 프롬프트

```text
첨부된 Workflow v1.4 문서를 기준으로, 현재 이 PC의 Claude Code에서 설정 사용 중인 MCP 설정을 확인해서 Workflow에 반영해줘.

목표:
- 현재 Claude Code에서 실제 적용 중인 MCP 서버 기준으로
  Confluence / Jira / Perforce MCP package 이름 또는 실행 방식을 확인한다.
- 확인된 내용을 Workflow 문서의 패키지 템플릿에 반영한다.
- token, password, 인증값은 절대 문서에 포함하지 않는다.

세부 요구사항:

1. 현재 Claude Code의 MCP 설정 방식을 확인해줘.
   - .mcp.json 방식인지
   - claude mcp add 명령으로 등록된 방식인지
   - 사용자 전역 설정인지
   - 프로젝트 단위 설정인지
   - 기타 Claude Code가 사용하는 MCP 설정 파일이 있는지 확인해줘.

2. 현재 등록된 MCP 서버 목록을 확인해줘.
   가능한 경우 아래 정보를 확인해줘.
   - MCP 서버 이름
   - command
   - args
   - cwd
   - env 또는 envFile 사용 여부
   - npx package 이름
   - node 실행 파일 경로
   - python 실행 파일 경로
   - 사내 npm registry 사용 여부
   - 실제 운영 중 여부

3. Confluence / Jira / Perforce MCP를 각각 분류해줘.

   예:
   - Confluence MCP
     - 실행 방식: npx package / node local file / python local file / 기타
     - package 이름 또는 실행 경로:
     - envFile:
     - 비고:

   - Jira MCP
     - 실행 방식:
     - package 이름 또는 실행 경로:
     - envFile:
     - 비고:

   - Perforce MCP
     - 실행 방식:
     - package 이름 또는 실행 경로:
     - envFile:
     - 비고:

4. 인증 정보는 마스킹해줘.
   아래 같은 문자는 절대 직접 쓰지 말.
   - CONFLUENCE_TOKEN
   - JIRA_TOKEN
   - P4PASSWD
   - password
   - token
   - secret
   - 개인 계정 정보

5. 현재 설정이 .env 파일을 사용하지 않고 직접 env 값을 가지고 있다면,
   최종 배포 구조에서는 C:\Users\<ID>\.mymcp\.env 를 참조하는 형태로 바꿀 수 있는지 검토해줘.

6. 현재 실행 중인 MCP 설정을 기준으로 아래 파일을 업데이트할 TODO를 작성해줘.
   - mcp-packages.template.md
   - docs/02_roo_setup_prompt.md
   - docs/03_claude_setup_prompt.md
   - docs/04_test_prompt.md
   - docs/05_troubleshooting.md
   - README.md

7. Workflow 문서에 반영할 "현재 MCP 실행 방식" 섹션을 작성해줘.

8. Roo Code에서도 동일한 MCP package 또는 실행 경로를 사용할 수 있는지 검토해줘.
   단, Roo Code 설정 파일은 Claude Code 설정 파일과 분리해서 관리해야 한다는 원칙을 유지해줘.

9. 최종 결과는 아래 형식으로 정리해줘.

   ## 1. 현재 Claude Code MCP 설정 확인 결과
   ## 2. Confluence/Jira/Perforce MCP 실행 방식
   ## 3. 문서에 반영할 package 이름 또는 실행 경로
   ## 4. 보안 문제에 들어올 수 있는 항목
   ## 5. Workflow v1.4 수정 필요 항목
   ## 6. ZIP 패키지에 포함할 파일별 TODO
   ## 7. 사용자 결정이 추가로 필요한 항목
```

---

## 사용 방법

1. 현재 PC에서 Claude Code를 실행한다.
2. `Roo Code + Claude Code 공통 MCP ZIP 배포 Workflow v1.4.md`를 첨부하거나 Claude Code가 읽을 수 있는 위치에 둔다.
3. 위 프롬프트를 Claude Code에 입력한다.
4. Claude Code가 현재 PC의 MCP 설정을 확인하도록 한다.
5. 결과에서 token, password, secret 값이 노출되지 않았는지 확인한다.
6. 확인된 package 이름 또는 실행 경로를 Workflow v1.4에 패키지 템플릿에 반영한다.

---

## 기대 결과

Claude Code가 아래 정보를 정리해야 한다.

```text
현재 Claude Code에서 확인 중인 MCP 설정
↓
설정 command / args / package 이름 / 실행 경로 추출
↓
token/password 제거
↓
mcp-packages.template.md에 기본값 반영
↓
Roo/Claude 설정 프롬프트에 "기본 package 이름"으로 반영
↓
최종 ZIP 패키지 생성
```

---

## 주의사항

- 설정 인증값을 문서에 포함하지 않는다.
- 사내 npm registry 주소가 보안상 민감하면 마스킹한다.
- 개인 계정명, token, password, secret은 절대 배포 ZIP에 포함하지 않는다.
- `.env.template`만 배포하고 `.env`는 개인 PC에서 생성한다.
- Claude Code 설정 파일과 Roo Code 설정 파일을 서로 분리해서 관리한다.
- 동일한 MCP package 또는 실행 경로를 사용할 수는 있지만, 각 도구의 설정 파일은 독립적으로 관리한다.
