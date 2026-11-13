# jsb-plus

『점프 투 스프링부트』(SBB)를 구현하면서 장희성 강사의 14163(점프 투 스프링부트 개선), 14168(JPA·타임리프 개선) 수업 내용을 적용한 프로젝트입니다.

- 진행 계획과 결정 사항: [docs/PLAN.md](docs/PLAN.md)
- 커밋 규칙: `기능번호(3자리)-세부번호(2자리) 기능명, 작업` + 같은 이름의 태그

## 작업내역

### 000-01 저장소 준비, git 초기화와 README 작성
- [링크](https://github.com/jhs512/jsb-plus/commit/000-01)
- 작업: git init (main 브랜치), .gitignore 기본 항목 추가, README 작업내역 섹션과 docs/PLAN.md 추가
- 이유: 작업 기록을 커밋 단위로 남기기 위한 준비, 커밋마다 README 작업내역을 누적하는 규칙 적용
- 결과: git log, git tag로 커밋과 태그 생성 확인

### 000-02 저장소 준비, GitHub 원격 저장소 연결
- [링크](https://github.com/jhs512/jsb-plus/commit/000-02)
- 작업: GitHub public 저장소 jhs512/jsb-plus 생성, origin 연결 후 main 브랜치와 태그 push
- 이유: README의 태그 링크(/commit/태그명)가 동작하려면 원격 저장소에 태그가 있어야 함
- 결과: gh repo view와 GitHub 태그 링크로 접속 확인

### 001-01 프로젝트 생성, Spring Initializr로 프로젝트 생성
- [링크](https://github.com/jhs512/jsb-plus/commit/001-01)
- 작업: Spring Boot 4.1.2, Java 25, Gradle Kotlin DSL(build.gradle.kts), 의존성: webmvc, lombok, devtools, 그룹 com.mysite, 메인 클래스 com.mysite.sbb.SbbApplication
- 이유: 교재(Boot 3.2.1, Groovy) 대신 14163 강의의 최신 환경 적용
- 결과: ./gradlew test 통과(contextLoads)

### 001-02 프로젝트 생성, application.yaml로 설정 파일 변경
- [링크](https://github.com/jhs512/jsb-plus/commit/001-02)
- 작업: application.properties를 application.yaml로 변경, 콘솔 컬러 출력(ansi) 활성화
- 이유: 계층 구조라 설정이 많아져도 읽기 쉬움(14163·14168 방식)
- 결과: ./gradlew test 통과

### 002-01 간단한 웹 프로그램, MainController와 void 액션 메서드 추가
- [링크](https://github.com/jhs512/jsb-plus/commit/002-01)
- 작업: MainController에 GET /, GET /hello 액션 메서드 추가(반환형 void)
- 이유: @Controller와 @GetMapping의 URL 매핑 동작 확인, void를 반환하면 응답 본문이 없다는 점을 직접 확인(14163 3강)
- 결과: / 와 /hello는 500(Circular view path), /not-exist는 404 응답 확인, 서버 콘솔에 index, hello 출력 확인

### 002-02 간단한 웹 프로그램, @ResponseBody로 문자열 응답
- [링크](https://github.com/jhs512/jsb-plus/commit/002-02)
- 작업: index(), hello()가 String을 반환하고 @ResponseBody 적용, MockMvc 컨트롤러 테스트(MainControllerTest) 추가
- 이유: 액션 메서드의 반환값을 HTTP 응답 본문으로 사용(14163 4강), 브라우저 대신 테스트로 응답 코드와 본문을 반복 검증
- 결과: ./gradlew test 4건 통과(/ -> SBB, /hello -> 안녕하세요., /not-exist -> 404)

### 003-01 DB 설정, H2와 JPA 추가 및 dev/test 프로파일 분리
- [링크](https://github.com/jhs512/jsb-plus/commit/003-01)
- 작업: data-jpa, h2, h2console 의존성 추가, application-dev.yaml(H2 파일 DB), application-test.yaml(H2 메모리 DB) 분리, DB 파일을 .gitignore에 추가, 테스트 클래스에 @ActiveProfiles("test") 적용
- 이유: 교재는 DB 하나만 사용하지만, 테스트가 개발 데이터를 오염시키지 않도록 분리(14163 5강), DB 파일은 저장소에 올리면 안 됨(14168 2강)
- 결과: ./gradlew test 통과, bootRun 후 /h2-console 200 응답, db_dev.mv.db 생성 및 git 추적 제외 확인

### 003-02 DB 설정, SQL 로그 출력 설정
- [링크](https://github.com/jhs512/jsb-plus/commit/003-02)
- 작업: show-sql, format_sql, highlight_sql, use_sql_comments 설정, 바인딩 파라미터(bind), 조회 값(extract), 트랜잭션 로그 TRACE 설정
- 이유: JPA가 만들어 내는 SQL과 실제 값을 눈으로 확인하며 학습(14163 6강)
- 결과: ./gradlew test 통과

### 004-01 엔티티, Question 엔티티 추가
- [링크](https://github.com/jhs512/jsb-plus/commit/004-01)
- 작업: Question 엔티티(id, subject, content, createDate) 추가
- 이유: 질문 데이터를 저장할 question 테이블을 JPA가 자동 생성하도록(교재 2-04)
- 결과: bootRun 로그에서 create table question(id identity, subject varchar(200), content TEXT, create_date) 확인

### 004-02 엔티티, Answer 엔티티와 ManyToOne(LAZY) 관계 추가
- [링크](https://github.com/jhs512/jsb-plus/commit/004-02)
- 작업: Answer 엔티티(id, content, createDate, question) 추가, @ManyToOne(fetch = LAZY)로 질문 참조
- 이유: 답변은 반드시 하나의 질문에 속함(N:1), ManyToOne 기본값 EAGER는 불필요한 조회를 유발하므로 LAZY 명시(14163 25강)
- 결과: DDL 로그에서 answer.question_id 컬럼과 question을 참조하는 외래키 제약 생성 확인

### 004-03 엔티티, [실험] mappedBy 없는 OneToMany는 중간 테이블을 만든다
- [링크](https://github.com/jhs512/jsb-plus/commit/004-03)
- 작업: Question에 @OneToMany List<Answer> answers 추가(mappedBy 없이)
- 이유: mappedBy가 없으면 JPA가 두 관계를 별개로 보고 중간 테이블을 만든다는 것을 직접 확인(14163 11강)
- 결과: DDL 로그에서 불필요한 question_answers(question_id, answers_id) 테이블 생성 확인
