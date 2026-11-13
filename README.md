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

### 005-06 리포지터리, 답변 생성 2가지 방법 비교(save vs addAnswer)
- [링크](https://github.com/jhs512/jsb-plus/commit/005-06)
- 작업: Question.addAnswer(content) 엔티티 메서드 추가, TestInitData의 답변 생성을 addAnswer로 변경, AnswerRepositoryTest: save 방식, addAnswer 방식, 양방향 조회 테스트
- 이유: 교재는 answerRepository.save를 사용, 강사는 객체지향적으로 question.addAnswer + cascade PERSIST(더티체킹으로 자동 INSERT) 사용(14163 21강)
- 결과: ./gradlew test 19건 통과, addAnswer 직후 id=0, flush 후 id 생성되는 것으로 INSERT 시점 확인

### 006-01 도메인별 분류, 14168 방식 패키지 구조로 이동
- [링크](https://github.com/jhs512/jsb-plus/commit/006-01)
- 작업: domain/{주제}/{엔티티}/{entity,repository,controller} 구조로 이동(question/question, question/answer, home/home), 공통 코드는 global/{jpa,initData}로 이동, 테스트도 같은 패키지 구조로 이동
- 이유: 기능이 늘어나도 파일 위치를 예측할 수 있도록 도메인 코드와 앱 전체 설정을 분리(14168 패키지 구조, PLAN 결정 1), 교재 2-06의 도메인별 분류를 한 단계 더 세분화
- 결과: ./gradlew test 19건 통과(이동만 했으므로 동작 변화 없음)

### 006-02 도메인별 분류, TestInitData를 BaseInitData로 이름 변경
- [링크](https://github.com/jhs512/jsb-plus/commit/006-02)
- 작업: TestInitData를 global/initData/BaseInitData로 이름 변경
- 이유: 이 샘플 데이터는 테스트뿐 아니라 개발 환경(dev)에서도 쓰이므로 이름을 용도에 맞게 변경(14168 2강)
- 결과: ./gradlew test 19건 통과

### 007-01 질문 목록, 타임리프 없이 질문 목록 출력
- [링크](https://github.com/jhs512/jsb-plus/commit/007-01)
- 작업: QuestionController에 GET /question/list 추가, 자바 문자열로 ul, li HTML을 직접 만들어 응답, 컨트롤러 테스트 추가
- 이유: 템플릿 엔진 없이 HTML을 만들면 얼마나 불편한지 먼저 경험(14163 27강)
- 결과: ./gradlew test 20건 통과(목록에 샘플 질문 제목 2개 포함)

### 007-02 질문 목록, 타임리프 도입과 Model로 데이터 전달
- [링크](https://github.com/jhs512/jsb-plus/commit/007-02)
- 작업: thymeleaf 의존성 추가, templates/question_list.html에서 th:each, th:text로 목록 출력, Model.addAttribute로 questionList 전달, dev 프로파일에서 타임리프 캐시 끄기, 소스 폴더 템플릿 직접 읽기
- 이유: HTML은 템플릿이, 데이터 준비는 컨트롤러가 맡도록 역할 분리(교재 2-07, 14163 28·30·31강), 템플릿 수정 후 재시작 없이 확인(14163 29강)
- 결과: ./gradlew test 20건 통과(같은 테스트가 템플릿 방식에서도 통과)

### 007-03 질문 목록, 테이블로 출력하고 작성일시 표시
- [링크](https://github.com/jhs512/jsb-plus/commit/007-03)
- 작업: question_list.html을 table(thead, tbody)로 변경, #temporals.format으로 작성일시 출력
- 이유: 목록 데이터는 표 형태가 읽기 쉬움(14163 32강, 교재 2-07)
- 결과: ./gradlew test 20건 통과, 브라우저에서 /question/list 표 출력 확인

### 008-01 루트 URL, / 접속 시 질문 목록으로 리다이렉트
- [링크](https://github.com/jhs512/jsb-plus/commit/008-01)
- 작업: GET / 가 redirect:/question/list 반환, 테스트를 302, Location 검증으로 변경
- 이유: 사이트 첫 화면을 질문 목록으로(교재 2-08), redirect는 서버가 직접 다른 페이지를 그리는 것이 아니라 브라우저에게 다시 요청하라고 알려 주는 것(14168 28강)
- 결과: ./gradlew test 20건 통과(GET / -> 302, redirectedUrl=/question/list)

### 009-01 서비스, QuestionService 도입
- [링크](https://github.com/jhs512/jsb-plus/commit/009-01)
- 작업: QuestionService(getList) 추가, 컨트롤러가 리포지터리 대신 서비스를 사용
- 이유: 컨트롤러(요청/응답), 서비스(업무 로직), 리포지터리(DB 접근) 역할 분리(교재 2-09), 여러 컨트롤러가 같은 로직을 재사용할 수 있음
- 결과: ./gradlew test 20건 통과

### 009-02 서비스, 엔티티 @Setter 제거하고 생성자와 의미 있는 메서드로 변경
- [링크](https://github.com/jhs512/jsb-plus/commit/009-02)
- 작업: Question, Answer의 @Setter 제거, @NoArgsConstructor(PROTECTED) + 필요한 값을 받는 생성자 추가, modify() 메서드 추가, addAnswer는 new Answer(this, content) 사용, BaseInitData, 테스트를 생성자/modify 방식으로 수정
- 이유: Setter가 열려 있으면 어디서든 값을 바꿀 수 있어 변경 지점을 추적하기 어려움, 값을 바꾸는 경로를 엔티티 메서드로 제한(14168 Post 엔티티 방식)
- 결과: ./gradlew test 20건 통과

### 010-01 상세 페이지, 질문을 조회해 상세 화면에 표시
- [링크](https://github.com/jhs512/jsb-plus/commit/010-01)
- 작업: QuestionService.getQuestion(id) 추가, GET /question/detail/{id}, question_detail.html 추가, 상세 화면 테스트 추가
- 이유: 질문 하나의 제목과 내용을 보여주는 화면(교재 2-10, 14163 35강 1단계), @PathVariable로 URL 경로의 값을 받는 방법 학습
- 결과: ./gradlew test 21건 통과

### 010-02 상세 페이지, 목록의 제목을 상세 페이지에 연결
- [링크](https://github.com/jhs512/jsb-plus/commit/010-02)
- 작업: question_list.html 제목에 th:href="@{, /question/detail/${question.id}, }" 링크 추가
- 이유: 목록에서 클릭해 상세로 이동(교재 2-10, 14163 35강 2단계), @{}(URL 생성)와 , , (문자열 치환) 문법 비교
- 결과: ./gradlew test 21건 통과(목록 HTML에 href=/question/detail/2 포함)

### 010-03 상세 페이지, 없는 질문에 404 응답
- [링크](https://github.com/jhs512/jsb-plus/commit/010-03)
- 작업: global/exception/DataNotFoundException(@ResponseStatus NOT_FOUND) 추가, getQuestion에서 findById().orElseThrow로 예외 발생, 404 테스트 추가
- 이유: findById().get()은 값이 없으면 500(No value present)이 나와 원인을 알기 어려움, 없는 자원은 404가 의미상 정확(14163 35강 3단계)
- 결과: ./gradlew test 22건 통과(/question/detail/999 -> 404)

### 011-01 URL 프리픽스, @RequestMapping("/question") 적용
- [링크](https://github.com/jhs512/jsb-plus/commit/011-01)
- 작업: QuestionController에 @RequestMapping("/question"), 액션 메서드는 /list, /detail/{id}만 표기
- 이유: 공통 URL 앞부분을 한 곳에서 관리(교재 2-11)
- 결과: ./gradlew test 22건 통과(URL 변화 없음)

### 012-01 답변 기능, 상세 화면에 답변 작성 폼 추가
- [링크](https://github.com/jhs512/jsb-plus/commit/012-01)
- 작업: question_detail.html에 textarea(name=content)와 POST 폼 추가
- 이유: 폼은 사용자가 입력한 값으로 요청(URL + 파라미터)을 만들어 주는 도구(14168 3강), 데이터를 생성하므로 GET이 아닌 POST(14168 7강)
- 결과: /question/detail/2 화면에 답변 폼 렌더링 확인(템플릿만 변경)

### 012-02 답변 기능, 답변 저장 처리
- [링크](https://github.com/jhs512/jsb-plus/commit/012-02)
- 작업: AnswerController: POST /answer/create/{id}, @Transactional, AnswerService.create -> question.addAnswer(content), 저장 후 상세 페이지로 redirect, 컨트롤러 테스트 추가
- 이유: 교재는 answerRepository.save를 쓰지만 강사처럼 OneToMany 필드로 생성(14163 37강), POST 처리 후 redirect로 새로고침 중복 등록 방지(14168 28강)
- 결과: ./gradlew test 23건 통과(302 /question/detail/2, 질문 2의 답변 목록에 새 답변 포함)

### 012-03 답변 기능, 상세 화면에 답변 개수와 목록 표시
- [링크](https://github.com/jhs512/jsb-plus/commit/012-03)
- 작업: question_detail.html에 #lists.size(question.answers)로 답변 개수, th:each로 답변 목록 출력, 상세 화면 테스트에 답변 개수와 내용 검증 추가
- 이유: OneToMany 필드 덕분에 리포지터리 없이 question.answers로 답변 목록 접근(14163 9강, 교재 2-12)
- 결과: ./gradlew test 23건 통과, 브라우저에서 답변 등록 후 상세 화면으로 돌아와 2개의 답변 표시 확인

### 013-01 웹 페이지 디자인, 스타일시트 적용
- [링크](https://github.com/jhs512/jsb-plus/commit/013-01)
- 작업: static/resource/style.css 추가(textarea 너비, 버튼 여백), 목록, 상세 템플릿에서 th:href="@{/resource/style.css}"로 연결
- 이유: 정적 파일(CSS, JS, 이미지)은 static 폴더에서 그대로 제공됨(교재 2-13), 나중에 시큐리티에서 /resource/** 한 줄로 허용하기 위해 처음부터 resource 하위에 둠(14168 46강)
- 결과: 앱 재시작 후 /resource/style.css 200 응답 확인, ./gradlew test 23건 통과

### 014-01 부트스트랩, cdnjs로 부트스트랩과 Pretendard 폰트 적용
- [링크](https://github.com/jhs512/jsb-plus/commit/014-01)
- 작업: 부트스트랩 5.3.8(cdnjs), Pretendard 폰트(jsDelivr)를 CDN 링크로 적용, 목록을 table 클래스, 상세를 card 클래스로 꾸밈, 본문 줄바꿈 유지(white-space: pre-line)
- 이유: 교재는 부트스트랩 파일을 내려받아 static에 넣지만, 강사처럼 CDN 주소로 사용해 저장소를 가볍게(14163 39강), 입력한 줄바꿈이 화면에서 사라지지 않도록(14168 29강)
- 결과: ./gradlew test 23건 통과, 브라우저에서 부트스트랩 카드 스타일 적용 확인

### 015-01 표준 HTML 구조, 레이아웃 템플릿 도입
- [링크](https://github.com/jhs512/jsb-plus/commit/015-01)
- 작업: thymeleaf-layout-dialect 의존성 추가, layout.html(doctype, head, CDN 링크, body) 추가, 목록, 상세는 layout:decorate와 layout:fragment="content"로 본문만 작성, layout:title-pattern으로 페이지별 title(예: 질문 목록 ,  SBB)
- 이유: 모든 페이지가 표준 HTML 구조를 갖추고, 공통 head를 한 곳에서 관리(교재 2-15), 페이지별 고유 title(14168 45강)
- 결과: ./gradlew test 23건 통과, 응답 HTML에 doctype, <title>질문 목록 ,  SBB</title>, 부트스트랩 링크 포함 확인

### 016-01 질문 등록, 등록 폼과 저장 처리
- [링크](https://github.com/jhs512/jsb-plus/commit/016-01)
- 작업: 목록에 질문 등록하기 버튼 추가, GET /question/create(폼), POST /question/create(저장 후 목록으로 redirect), QuestionService.create 추가, question_form.html 추가(label 안에 input을 넣는 방식), 등록 테스트 추가
- 이유: 같은 URL을 GET(폼 보여주기), POST(처리)로 구분(14168 26강), action 생략 시 현재 URL로 전송(14168 26강), label 안에 input을 넣는 현대적 방식(14168 21강)
- 결과: ./gradlew test 25건 통과(등록 후 목록에 새 질문 제목 표시)

### 016-02 질문 등록, record 폼과 @Valid 검증
- [링크](https://github.com/jhs512/jsb-plus/commit/016-02)
- 작업: validation 의존성 추가, QuestionForm(record) + @NotBlank, @Size, @ModelAttribute("form") @Valid + BindingResult로 실패 시 폼 다시 표시, th:object, th:field로 입력값 유지, #fields.allErrors로 에러 출력
- 이유: 교재의 class + Getter/Setter 폼 대신 record로 코드 축소(14168 40강), BindingResult가 없으면 검증 실패 시 바로 400 예외(14168 13강), 에러 메시지에 순번-필드명을 붙여 다음 작업에서 정렬, focus에 사용
- 결과: ./gradlew test 26건 통과(빈 제목 -> 200 question_form, subject 필드 에러, 입력한 내용 유지), 현재 화면에는 01-subject- 접두어가 그대로 보임(다음 커밋에서 처리)

### 016-03 질문 등록, 에러 메시지 프래그먼트(정렬, 첫 에러 필드 focus)
- [링크](https://github.com/jhs512/jsb-plus/commit/016-03)
- 작업: form_errors.html 프래그먼트: 순번으로 정렬, 순번-필드명 접두어 제거 후 출력, data-error-field-name 속성, 첫 에러 필드 focus 스크립트를 document.currentScript.closest('form') + DOMContentLoaded 방식으로 구현
- 이유: 여러 폼에서 재사용하는 에러 표시(14168 23~25강), 강사 방식(마지막 form + setTimeout)은 프래그먼트가 폼 맨 위에 있으면 입력칸이 아직 없어 콘솔 에러가 남(브라우저에서 확인 후 개선)
- 결과: ./gradlew test 26건 통과, 브라우저: 빈 폼 제출 시 메시지 2개가 순번대로 표시, 제목만 입력 후 제출 시 입력값 유지, 커서가 내용 textarea로 이동, 콘솔 에러 없음

### 016-04 질문 등록, 답변 폼에도 검증 적용
- [링크](https://github.com/jhs512/jsb-plus/commit/016-04)
- 작업: AnswerForm(record, @NotBlank) + @Valid, BindingResult, 검증 실패 시 question을 모델에 담아 상세 화면 다시 표시, 상세 화면 답변 폼에 th:object=answerForm, th:field, 에러 프래그먼트 적용, 빈 답변 테스트 추가
- 이유: 빈 답변이 저장되지 않도록 서버에서 반드시 검증(14168 35강), 질문 폼과 같은 에러 표시 방식 재사용
- 결과: ./gradlew test 27건 통과(공백 답변 -> question_detail 다시 표시, 답변 수 변화 없음)

### 017-01 내비게이션 바, 레이아웃에 navbar 프래그먼트 추가
- [링크](https://github.com/jhs512/jsb-plus/commit/017-01)
- 작업: navbar.html(th:fragment) 추가: 로고(SBB), 질문 목록 링크, 좁은 화면용 토글 버튼, layout.html에서 th:replace로 모든 페이지에 포함
- 이유: 모든 페이지 공통 메뉴를 한 파일에서 관리(교재 3-01)
- 결과: ./gradlew test 27건 통과, /question/list 응답에 navbar-brand 포함 확인

### 018-01 페이징, Pageable로 질문 목록 페이징
- [링크](https://github.com/jhs512/jsb-plus/commit/018-01)
- 작업: QuestionService.getList(page): PageRequest.of(page, 10, 작성일 내림차순), 컨트롤러에서 page 파라미터(기본값 0)를 받아 Page 객체 전달, 목록 템플릿에 이전/다음, 페이지 번호(현재 기준 앞뒤 5개) 추가, 페이징 테스트 추가
- 이유: 글이 많아져도 한 번에 10개씩만 조회(LIMIT/OFFSET)(교재 3-02), 최신 글이 먼저 보이도록 정렬
- 결과: ./gradlew test 28건 통과(27개 중 1페이지에 최신 글, 3페이지에 가장 오래된 글)
