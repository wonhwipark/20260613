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
- 단, Roo Code 설정 파일과 Claude Code 설정 파일은 각각 별도로 관리한다.
