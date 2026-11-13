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

### 004-04 엔티티, mappedBy와 cascade로 OneToMany 완성
- [링크](https://github.com/jhs512/jsb-plus/commit/004-04)
- 작업: @OneToMany(mappedBy = "question", cascade = {PERSIST, REMOVE}), answers를 new ArrayList<>()로 초기화
- 이유: 관계의 주인을 Answer.question으로 지정해 중간 테이블 제거(14163 12강), 답변 자동 저장, 삭제를 위한 cascade(14163 21강)
- 결과: DDL 로그에서 question_answers 테이블이 사라짐 확인

### 004-05 엔티티, BaseEntity와 JPA Auditing 도입
- [링크](https://github.com/jhs512/jsb-plus/commit/004-05)
- 작업: BaseEntity(@MappedSuperclass: id, createDate, modifyDate) 추가, Question, Answer가 BaseEntity 상속, @EnableJpaAuditing으로 생성일, 수정일 자동 기록
- 이유: 교재는 엔티티마다 id, 날짜를 반복 선언하고 createDate를 직접 대입함, 공통 필드를 한 곳에서 관리하고 날짜는 자동으로(14168 2강)
- 결과: DDL에서 두 테이블 모두 id, create_date, modify_date 컬럼 생성 확인, ./gradlew test 통과

### 005-01 리포지터리, Repository 추가와 샘플 데이터 자동 생성
- [링크](https://github.com/jhs512/jsb-plus/commit/005-01)
- 작업: QuestionRepository, AnswerRepository(JpaRepository) 추가, TestInitData: ApplicationRunner 빈으로 시작 시 질문 2개, 답변 1개 생성, @Lazy self 주입으로 @Transactional 적용
- 이유: 교재는 테스트 코드로 데이터를 넣지만, 앱 시작마다 같은 샘플 데이터가 보장되도록(14163 14강), this.work1() 직접 호출은 프록시를 거치지 않아 트랜잭션이 적용되지 않음
- 결과: bootRun 로그에서 work1 트랜잭션 하나 안에서 count 1회, insert 3회 확인, ./gradlew test 통과

### 005-02 리포지터리, findAll과 findById 테스트
- [링크](https://github.com/jhs512/jsb-plus/commit/005-02)
- 작업: QuestionRepositoryTest 추가(findAll, findById)
- 이유: 샘플 데이터가 메모리 DB에 들어가는지, Repository 기본 메서드가 동작하는지 테스트로 확인(교재 2-05, 14163 15강)
- 결과: ./gradlew test 6건 통과

### 005-03 리포지터리, 쿼리 메서드 명명 규칙
- [링크](https://github.com/jhs512/jsb-plus/commit/005-03)
- 작업: findBySubject, findBySubjectAndContent, findBySubjectLike, findBySubjectIn, findByIdLessThanOrderByIdDesc 추가 및 테스트
- 이유: 메서드 이름만으로 WHERE, AND, LIKE, IN, ORDER BY 조건이 생성되는 규칙 학습(교재 2-05, 14163 16·17강)
- 결과: ./gradlew test 11건 통과, 테스트 로그에서 생성된 SQL 확인

### 005-04 리포지터리, [실험] 트랜잭션 밖 LAZY 로딩은 실패한다
- [링크](https://github.com/jhs512/jsb-plus/commit/005-04)
- 작업: 트랜잭션 없이 question.getAnswers()에 접근하면 LazyInitializationException이 발생함을 검증하는 테스트 추가
- 이유: LAZY는 실제로 필요할 때 SELECT 하지만, 그때 DB 연결이 열려 있어야 함을 확인(14163 25강)
- 결과: ./gradlew test 12건 통과(예외 발생을 assertThatThrownBy로 확인)

### 005-05 리포지터리, @Transactional로 LAZY 로딩과 수정·삭제 테스트
- [링크](https://github.com/jhs512/jsb-plus/commit/005-05)
- 작업: @Transactional 테스트로 LAZY 컬렉션 조회 성공 확인, 수정(더티체킹 + flush), 삭제, cascade REMOVE 테스트 추가
- 이유: 테스트 메서드 전체를 트랜잭션으로 묶으면 DB 연결이 유지되고, 끝나면 롤백되어 테스트끼리 독립(14163 18·25강)
- 결과: ./gradlew test 16건 통과, 수정 테스트 이후에도 다른 테스트가 원래 제목을 조회함(롤백 확인)
