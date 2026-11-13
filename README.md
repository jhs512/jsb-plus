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

### 018-02 페이징, 개발용 대량 데이터 생성(DevInitData)
- [링크](https://github.com/jhs512/jsb-plus/commit/018-02)
- 작업: @Profile("dev") DevInitData: 질문 300개 생성, @Order로 BaseInitData(1) -> DevInitData(2) 실행 순서 지정
- 이유: 페이징을 눈으로 확인하려면 데이터가 많아야 함(교재 3-02), 교재처럼 테스트 코드로 넣으면 개발 DB를 건드리게 되므로 dev 전용 초기 데이터로 분리, test 프로파일은 그대로 2개 유지
- 결과: ./gradlew test 28건 통과(test에는 영향 없음), 빈 dev DB로 실행 후 1페이지에 테스트 데이터 10개, 31페이지에 sbb가 무엇인가요? 표시 확인

### 019-01 게시물 번호, 전체 개수 기준 역순 번호
- [링크](https://github.com/jhs512/jsb-plus/commit/019-01)
- 작업: 번호 = totalElements - (number * size) - loop.index
- 이유: loop.count는 페이지마다 1부터 다시 시작함, 최신 글이 가장 큰 번호를 갖도록 계산(교재 3-03)
- 결과: dev DB(302개)에서 1페이지 첫 줄 302, 마지막 페이지 마지막 줄 1 확인, ./gradlew test 28건 통과

### 020-01 답변 개수, 목록에 답변 개수 표시
- [링크](https://github.com/jhs512/jsb-plus/commit/020-01)
- 작업: 질문 제목 옆에 #lists.size(question.answers) 표시(답변이 있을 때만)
- 이유: 목록에서 답변이 달린 질문을 바로 알아볼 수 있도록(교재 3-04)
- 결과: ./gradlew test 28건 통과, 목록 1페이지 요청 시 SQL 로그에서 질문 10개마다 answer 조회가 1번씩, 총 10번 실행됨(N+1 문제) 확인

### 020-02 답변 개수, default_batch_fetch_size로 N+1 완화
- [링크](https://github.com/jhs512/jsb-plus/commit/020-02)
- 작업: hibernate.default_batch_fetch_size: 100 설정
- 이유: 목록의 질문마다 answers를 따로 조회하는 N+1(10번 추가 조회) 문제 확인, LAZY 컬렉션을 IN 절로 묶어 한 번에 조회(강의 외 추가 개선, 14163 13강의 OneToMany 사용 기준과 연결)
- 결과: ./gradlew test 28건 통과, 목록 1페이지 SQL 로그에서 answer 조회가 question_id in (...) 1번으로 줄어듦 확인

### 021-01 스프링 시큐리티, 시큐리티 도입과 SecurityFilterChain 설정
- [링크](https://github.com/jhs512/jsb-plus/commit/021-01)
- 작업: spring-boot-starter-security(+test) 의존성 추가, SecurityConfig: /resource/**, /h2-console/**, 그 외 모두 permitAll, H2 콘솔용 frameOptions sameOrigin, CSRF 예외, 테스트의 POST 요청에 .with(csrf()) 추가
- 이유: 의존성만 추가하면 모든 페이지가 로그인 페이지로 리다이렉트(302 /login)되는 기본 동작 확인 후 설정(14168 46강), 교재의 AntPathRequestMatcher는 Security 7에서 제거되어 문자열 패턴 사용, CSRF: 다른 사이트가 사용자 몰래 POST 요청을 보내는 공격 방지
- 결과: 의존성만 추가 시 GET /question/list -> 302 /login 확인, 설정 후 GET 200, 토큰 없는 POST 403 확인, th:action 폼(답변)에는 _csrf hidden 자동 삽입, action 생략 폼(질문 등록)에는 없음 확인(다음 커밋에서 처리), ./gradlew test 28건 통과

### 021-02 스프링 시큐리티, action 없는 폼에 CSRF 토큰 추가
- [링크](https://github.com/jhs512/jsb-plus/commit/021-02)
- 작업: question_form.html에 _csrf hidden input 직접 추가, layout.html head에 <meta name="_csrf"> 추가(이후 자바스크립트 요청용)
- 이유: th:action을 쓰지 않는 폼은 토큰이 자동 삽입되지 않아 질문 등록이 403으로 막힘(14168 46강)
- 결과: curl로 등록 폼에서 받은 토큰과 쿠키를 함께 보내면 302 /question/list(등록 성공) 확인, ./gradlew test 28건 통과

### 022-01 회원가입, SiteUser 엔티티와 UserRepository 추가
- [링크](https://github.com/jhs512/jsb-plus/commit/022-01)
- 작업: SiteUser(username, password, email, username과 email은 unique) 추가, UserRepository(findByUsername, existsByUsername, existsByEmail) 추가
- 이유: 회원 정보를 저장할 테이블(교재 3-06), user는 DB 예약어라 SiteUser로 명명
- 결과: DDL 로그에서 site_user 테이블과 unique 제약 생성 확인, ./gradlew test 28건 통과

### 022-02 회원가입, BCrypt 비밀번호 암호화와 UserService
- [링크](https://github.com/jhs512/jsb-plus/commit/022-02)
- 작업: SecurityConfig에 PasswordEncoder(BCryptPasswordEncoder) 빈 추가, UserService.create: 비밀번호를 암호화해서 저장, UserServiceTest 추가
- 이유: 비밀번호 원문을 저장하면 DB 유출 시 그대로 노출됨(교재 3-06), BCrypt는 salt를 섞어 같은 비밀번호도 매번 다른 해시가 됨
- 결과: ./gradlew test 29건 통과(저장된 비밀번호가 원문과 다르고 $2a$ 형식, matches로 검증 성공)

### 022-03 회원가입, 회원가입 폼과 검증
- [링크](https://github.com/jhs512/jsb-plus/commit/022-03)
- 작업: UserController: GET/POST /user/signup, UserCreateForm(record), 비밀번호 확인 불일치, 사용자ID·이메일 중복을 rejectValue로 필드 에러 처리, signup_form.html(에러 프래그먼트 재사용), navbar에 회원가입 링크, UserControllerTest 4건
- 이유: 교재 3-06 회원가입, 교재는 저장 시 DB 예외를 잡아 전체 에러로 보여주지만, 저장 전에 중복을 확인해 어느 필드가 문제인지 알려주도록 개선, 에러 메시지는 순번-필드명-내용 형식을 그대로 사용해 정렬과 focus 유지
- 결과: ./gradlew test 33건 통과(성공 302 /, 비밀번호 불일치, 중복 ID, 이메일 형식 오류)

### 023-01 로그인과 로그아웃, 폼 로그인 구현
- [링크](https://github.com/jhs512/jsb-plus/commit/023-01)
- 작업: UserRole(ADMIN, USER), UserSecurityService(UserDetailsService) 추가, SecurityConfig에 formLogin(/user/login), logout(POST /user/logout) 설정, login_form.html, 실패 시 ?error 메시지, navbar: sec:authorize로 로그인 상태별 메뉴, 사용자 이름 표시, 로그아웃 버튼, BaseInitData에 개발용 계정(admin, user1, user2) 추가, 로그인/로그아웃 테스트 4건
- 이유: 교재 3-07 로그인과 로그아웃, 교재는 GET으로 로그아웃하지만 Security 7 기본값대로 CSRF 보호를 받는 POST 사용
- 결과: ./gradlew test 37건 통과(user1 로그인 USER 권한, admin ADMIN 권한, 잘못된 비밀번호 ?error, 로그아웃), 브라우저에서 user1 로그인 후 목록으로 이동, 상단에 user1과 로그아웃 표시 확인

### 024-01 글쓴이, 질문과 답변에 author 저장, 로그인 필수
- [링크](https://github.com/jhs512/jsb-plus/commit/024-01)
- 작업: Question.author, Answer.author(@ManyToOne LAZY SiteUser) 추가, 생성자와 addAnswer에 author 인자, @EnableMethodSecurity + 질문 등록, 답변 등록에 @PreAuthorize("isAuthenticated()"), Principal로 로그인 사용자 조회(UserService.getUser), 샘플 데이터 글쓴이 지정, 테스트는 @WithUserDetails로 로그인 상태 실행
- 이유: 누가 쓴 글인지 저장(교재 3-08), 로그인하지 않은 사용자는 글을 쓸 수 없게
- 결과: ./gradlew test 39건 통과(비로그인 GET /question/create -> /user/login 리다이렉트, 등록한 질문의 author = user1)

### 024-02 글쓴이, 목록과 상세에 글쓴이 표시
- [링크](https://github.com/jhs512/jsb-plus/commit/024-02)
- 작업: 목록에 글쓴이 열 추가, 상세의 질문, 답변 카드에 글쓴이 표시(author?.username), 비로그인 사용자에게는 답변 입력칸을 비활성화하고 안내 문구 표시
- 이유: 누가 쓴 글인지 화면에 보여주기(교재 3-08), ?. 안전 접근으로 author가 없는 예전 데이터도 에러 없이 표시(14168 16강)
- 결과: ./gradlew test 39건 통과, 빈 dev DB로 실행 후 상세 화면에 user1(질문), user2(답변), 비로그인 안내 문구 확인

### 025-01 수정과 삭제, _method 필터와 global.js 도입
- [링크](https://github.com/jhs512/jsb-plus/commit/025-01)
- 작업: spring.mvc.hiddenmethod.filter.enabled: true, static/resource/global.js: a[method=POST, PUT, DELETE] 클릭 시 CSRF 토큰과 _method를 담은 숨은 폼으로 전송, onclick(confirm) 먼저 실행, layout.html에서 global.js 로드, 로그아웃을 a[method=POST] 링크로 변경
- 이유: 교재는 삭제, 추천을 GET 링크로 처리하는데, GET은 조회용이라 링크 미리보기나 크롤러 요청, CSRF 공격으로 데이터가 바뀔 수 있음(PLAN 결정 2), HTML 폼은 GET, POST만 지원하므로 _method로 PUT, DELETE 표현(14168 41~44강)
- 결과: ./gradlew test 39건 통과, 브라우저에서 user1 로그인 후 로그아웃 링크 클릭 시 POST /user/logout 처리되어 메뉴가 로그인, 회원가입으로 바뀜 확인

### 025-02 수정과 삭제, 질문 수정(PUT)과 삭제(DELETE)
- [링크](https://github.com/jhs512/jsb-plus/commit/025-02)
- 작업: Question.isAuthor(user), QuestionService.modify, delete 추가, GET /question/modify/{id}(기존 값 채운 폼), PUT /question/modify/{id}, DELETE /question/delete/{id}, 등록 폼 재사용: 수정일 때만 _method=PUT hidden, 제목/타이틀을 질문 수정으로, 본인 글이 아니면 403, 상세 화면에서 본인에게만 수정·삭제 버튼(삭제는 a[method=DELETE] + confirm), 테스트 6건 추가
- 이유: 교재 3-09 수정과 삭제, 교재의 POST 수정, GET 삭제 대신 PUT, DELETE(PLAN 결정 2, 14168 41·44강), 권한 없음은 400보다 403 Forbidden이 정확(강의 외 추가 개선)
- 결과: ./gradlew test 45건 통과(본인 수정 302, POST+_method=PUT 동작, 남의 글 403, DELETE 후 상세 404, GET 삭제 405), 브라우저에서 user1로 수정 폼(기존 값 채워짐) -> 저장 -> 상세에 (수정됨) 제목, 수정·삭제 버튼 표시 확인

### 025-03 수정과 삭제, 답변 수정(PUT)과 삭제(DELETE)
- [링크](https://github.com/jhs512/jsb-plus/commit/025-03)
- 작업: AnswerService.getAnswer, modify, delete 추가, Answer.isAuthor 추가, GET/PUT /answer/modify/{id}, answer_form.html, DELETE /answer/delete/{id}, 삭제는 question.deleteAnswer(answer) -> @OneToMany orphanRemoval = true로 DELETE, BaseEntity에 id 기반 equals/hashCode(동치성 먼저 비교), 상세 화면에서 본인 답변에만 수정·삭제 버튼, 테스트 3건
- 이유: 교재 3-09 답변 수정과 삭제, answerRepository.delete 대신 부모 컬렉션에서 제거하는 객체지향 방식(14168 37강), 리스트의 remove가 같은 id의 답변을 찾으려면 equals 재정의 필요(14168 37·38강)
- 결과: ./gradlew test 48건 통과(본인 수정 302, 남의 답변 403, 삭제 후 answer 조회 결과 없음)

### 025-04 수정과 삭제, 수정일시 표시
- [링크](https://github.com/jhs512/jsb-plus/commit/025-04)
- 작업: 상세 화면의 질문, 답변 카드에 modifyDate가 createDate보다 나중일 때만 modified at 배지 표시
- 이유: 교재는 modifyDate가 null이 아니면 표시하지만, Auditing은 생성 시에도 modifyDate를 채우므로 두 시각을 비교(교재 3-09 응용)
- 결과: ./gradlew test 48건 통과, 브라우저에서 수정한 2번 질문에만 modified at 표시, 수정하지 않은 답변에는 표시 안 됨 확인

### 026-01 추천, 질문과 답변 추천(POST)
- [링크](https://github.com/jhs512/jsb-plus/commit/026-01)
- 작업: Question, Answer에 @ManyToMany Set<SiteUser> voters와 vote() 추가(question_voters, answer_voters 중간 테이블), POST /question/vote/{id}, POST /answer/vote/{id}, 상세 화면에 추천 버튼(a[method=POST] + confirm)과 추천 수, 테스트 2건
- 이유: 교재 3-10 추천 기능, 교재의 GET 추천 대신 POST(PLAN 결정 2), Set + id 기반 equals로 같은 사람의 중복 추천 방지
- 결과: ./gradlew test 50건 통과(같은 사용자가 두 번 추천해도 1표), DDL에서 question_voters(question_id, voters_id) 중간 테이블 생성 확인

### 027-01 앵커, 답변 작성·수정·추천 후 해당 답변 위치로 이동
- [링크](https://github.com/jhs512/jsb-plus/commit/027-01)
- 작업: 상세 화면 답변마다 <a id="answer_{id}"> 앵커, 답변 작성, 수정, 추천 후 /question/detail/{질문id}#answer_{답변id}로 redirect, 답변 작성 시 flush로 INSERT를 먼저 실행해 새 답변 id 확보
- 이유: 답변이 많을 때 페이지 맨 위로 돌아가지 않고 방금 작업한 답변으로 스크롤(교재 3-11), addAnswer 방식은 트랜잭션 종료 시 INSERT라서 그 전에는 id가 0(14163 21강에서 본 동작)
- 결과: ./gradlew test 50건 통과(작성·수정·추천은 #answer_* 패턴, 삭제는 앵커 없이 상세로)

### 028-01 마크다운, commonmark로 질문·답변 본문 렌더링
- [링크](https://github.com/jhs512/jsb-plus/commit/028-01)
- 작업: commonmark 0.24.0 의존성 추가, global/markdown/MarkdownService(render) 추가, @ControllerAdvice GlobalModelAttribute로 모든 템플릿에 markdown 변수 등록, 상세 화면 본문을 th:utext="${markdown.render(...)}"로 출력, MarkdownServiceTest 2건
- 이유: 본문에 제목, 목록, 굵게 등 서식 사용(교재 3-12), 교재의 ${@빈.메서드()} 방식은 Thymeleaf 3.1.5에서 TemplateProcessingException으로 막혀서 공통 모델 속성으로 전달(14168 19강), [실험] 입력 속 <script>가 그대로 HTML이 되는 것을 테스트로 확인(다음 커밋에서 막음)
- 결과: ./gradlew test 52건 통과(마크다운 -> h1, strong, li 변환, script 태그가 그대로 통과함을 확인)

### 028-02 마크다운, 원본 HTML 이스케이프와 URL 무력화로 XSS 방지
- [링크](https://github.com/jhs512/jsb-plus/commit/028-02)
- 작업: HtmlRenderer에 escapeHtml(true), sanitizeUrls(true) 적용, 테스트: script 태그 이스케이프, javascript: 링크 무력화
- 이유: th:utext는 이스케이프를 하지 않으므로 사용자가 입력한 HTML이 그대로 실행될 수 있음(14168 16강 th:text vs th:utext), 교재 코드에는 이 처리가 없음(강의 외 추가 개선)
- 결과: ./gradlew test 53건 통과(<script> -> &lt;script&gt;, href=javascript: 없음)

### 029-01 검색, JPQL로 질문·답변·글쓴이 통합 검색 쿼리
- [링크](https://github.com/jhs512/jsb-plus/commit/029-01)
- 작업: QuestionRepository.findAllByKeyword: @Query JPQL(left join + distinct + like), Pageable, QuestionService.getList(page, kw) 추가, 검색 테스트(제목, 내용, 질문 글쓴이, 답변 내용, 답변 글쓴이, 빈 검색어, 결과 없음)
- 이유: 교재 3-13 검색, 조건이 많아 메서드 이름 규칙 대신 JPQL 사용, 답변이 없는 질문도 나와야 하므로 LEFT JOIN, 답변 여러 개가 일치해도 질문은 한 번만 나오도록 DISTINCT
- 결과: ./gradlew test 54건 통과

### 029-02 검색, 검색창과 검색어 유지 페이징
- [링크](https://github.com/jhs512/jsb-plus/commit/029-02)
- 작업: 컨트롤러에 kw 파라미터(기본값 빈 문자열) 추가, 모델에 kw 전달, 목록 상단에 GET 검색 폼, 질문 등록 버튼을 위로 이동, 페이지 링크를 @{/question/list(page=..., kw=...)}로 만들어 검색어 유지, 컨트롤러 검색 테스트
- 이유: 교재 3-13 검색 화면, 교재는 숨은 폼과 자바스크립트로 page, kw를 함께 보내지만, 검색은 조회라 GET 폼으로 충분하고 링크는 타임리프 URL 파라미터 문법으로 인코딩까지 처리
- 결과: ./gradlew test 55건 통과, 브라우저에서 [01 검색 시 테스트 데이터 [010]~[019] 10건, 검색창에 검색어 유지 확인

### 030-01 답변 페이징과 정렬, 상세 화면 답변 5개씩, 최신순/추천순
- [링크](https://github.com/jhs512/jsb-plus/commit/030-01)
- 작업: AnswerRepository: findByQuestion(Pageable), 추천순 JPQL(order by size(a.voters) desc + countQuery), AnswerService.getList(question, page, sort), 상세 화면: answerPaging으로 출력, 정렬 select(global.js의 select[value]), 답변 페이지 링크(answerPage, answerSort 유지), AnswerServiceTest
- 이유: 도전과제 3-14 답변 페이징과 정렬 기능, 답변이 많으면 question.answers 전체 로딩은 메모리 부담(14163 13강: 메이저급은 댓글도 페이징), 추천 수는 컬럼이 아니라 중간 테이블 크기라서 Sort 대신 JPQL size() 사용
- 결과: ./gradlew test 56건 통과(답변 7개 -> 2페이지, 최신순 첫 답변 = 답변 7, 추천순 = 답변 3(2표), 답변 5(1표) 순)

### 031-01 댓글, 질문·답변 댓글 작성과 삭제
- [링크](https://github.com/jhs512/jsb-plus/commit/031-01)
- 작업: Comment 엔티티(author, content, question 또는 answer) 추가, Question, Answer에 comments(@OneToMany PERSIST, REMOVE, orphanRemoval)와 addComment, deleteComment, CommentController: POST /comment/create/question/{id}, /comment/create/answer/{id}, DELETE /comment/delete/{id}, comment_fragment.html(질문, 답변 공용) + global.js의 Comment__submitWriteForm 클라이언트 검증, CommentControllerTest 4건
- 이유: 도전과제 3-14 댓글 기능, 14168 PostComment 방식(엔티티 메서드로 추가, 부모 목록에서 제거하면 orphanRemoval로 삭제), 클라이언트 검증은 빠른 피드백, 서버 검증은 필수(14168 35·36강)
- 결과: ./gradlew test 60건 통과(질문 댓글 표시, 답변 댓글 후 #answer_1 이동, 빈 댓글 400, 남의 댓글 삭제 403, 본인 삭제 후 DB에서 사라짐)

### 032-01 카테고리, 질문 카테고리(질문답변/자유게시판/강좌)
- [링크](https://github.com/jhs512/jsb-plus/commit/032-01)
- 작업: Category 엔티티(code, name), CategoryRepository, CategoryService 추가, Question.category(@ManyToOne LAZY), 생성자와 modify에 category, 검색 JPQL에 (:category is null or q.category = :category) 조건, 목록: 카테고리 탭, 배지, 페이지·검색 링크에 category 유지, 질문 폼: 카테고리 select(필수), 내비게이션 바에 카테고리 메뉴, GlobalModelAttribute에 categories, 샘플 데이터: 카테고리 3개, 샘플 질문은 qna, dev 대량 데이터는 free
- 이유: 도전과제 3-14 카테고리 기능, 게시판 종류를 코드가 아닌 데이터(엔티티)로 관리해 추가가 쉽도록, URL에는 id 대신 의미 있는 code 사용
- 결과: ./gradlew test 61건 통과(qna 필터에 샘플 질문, free 필터에 없음, 수정 시 카테고리 변경), 브라우저에서 카테고리 탭(전체/질문답변/자유게시판/강좌)과 질문답변 필터 결과 2건 확인
