%% LLM 리뷰봇 UML 액티비티 다이어그램
%% Mermaid JS Code for Activity Diagram
```mermaid
activityDiagram
    
    %% 1. 수영장 정의 (Swimlanes)
    
    %% GitHub 이벤트가 트리거 역할
    
    participant "GitHub Actions Runner" as GHA
    participant "LLM Agent (Python)" as LLM
    participant "GitHub API" as API

    %% 2. 시작 및 이벤트 트리거
    
    start
    -> PR 생성 (Pull Request)
    
    %% 3. GitHub Actions (환경 준비 및 실행)
    
    GHA -> GHA: 코드 체크아웃 및 환경 설정
    GHA -> LLM: 실행 명령어 전달 (python3 review_agent.py)
    
    %% 4. LLM Agent (리뷰 생성 및 인코딩)
    
    LLM -> LLM: 저장소별 설정 파일 로드
    LLM -> LLM: LLM API 호출 및 리뷰 텍스트 생성
    
    LLM --> GHA: 출력 값 전달 (Base64 인코딩된 문자열)
    
    %% 5. GitHub Actions (결정 및 코멘트 포스트)
    
    GHA -> GHA: Base64 문자열을 출력 변수에 설정 ($GITHUB_OUTPUT)

    GHA -> GHA: **결정: 리뷰 내용이 유효한가?**
    
    alt 내용 없음 (스킵)
        GHA -> GHA: [Yes] 액션 건너뛰기
        GHA -> end
    else 내용 있음 (포스트 진행)
        GHA -> GHA: [No] 다음 스텝 진행
        
        GHA -> GHA: actions/github-script 실행
        GHA -> GHA: Base64 문자열 디코딩 및 코멘트 본문 구성 (JavaScript)
        
        GHA -> API: **API 호출:** issues.createComment(PR_NUMBER, BODY)
        
        API -> API: 코멘트 생성 처리
        API -> GHA: 응답 (201 Created)
```        
        GHA -> end
    end
