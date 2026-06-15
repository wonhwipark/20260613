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

