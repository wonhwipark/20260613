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

