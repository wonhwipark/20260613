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

