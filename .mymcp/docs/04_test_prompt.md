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

