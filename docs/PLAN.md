# jsb-plus 진행 계획

> 최상위 목표: **이 과정을 통해 내가 스프링부트·JPA·타임리프를 이해하는 것.**
> 기능 완성은 그 결과일 뿐입니다. 이해가 안 됐다면 다음 작업으로 넘어가지 않습니다.

---

## 1. 분석한 자료 요약

### 1-1. 교재: 점프 투 스프링부트 (sbb3)
- 스택: Spring Boot **3.2.1**, Gradle(Groovy), `application.properties`, H2 파일 DB(`~/local`) 하나만 사용
- 패키지: `com.mysite.sbb.question / answer / user` (도메인별 패키지 하나에 Entity·Repository·Service·Controller가 함께 있음)
- 엔티티: `@Getter @Setter`, `createDate`는 서비스에서 `LocalDateTime.now()`로 직접 넣음, `@ManyToOne`은 기본값(EAGER)
- 답변 생성: `answerRepository.save(answer)` 방식
- 폼: `QuestionForm` 같은 클래스(`@Getter @Setter`) + `@Valid` + `BindingResult`
- 삭제·추천: `GET /question/delete/{id}`, `GET /question/vote/{id}`처럼 **GET으로 상태를 바꿈**
- 권한 체크: 컨트롤러에서 `username.equals(...)`로 비교하고, 실패하면 `400 BAD_REQUEST`
- 시큐리티: `AntPathRequestMatcher`를 사용합니다. 이 클래스는 **Boot 4 / Security 7에서 제거**되어 그대로 쓸 수 없습니다.
- 구성: 2장(기본 기능 2-02 ~ 2-16), 3장(서비스 기능 3-01 ~ 3-13), 3-14(추가 기능 도전과제), 4장(배포)

### 1-2. 강의 14163: 점프 투 스프링부트 개선 (`p-14163-2`, 교재 2-02 ~ 2-14 구간)
| 항목 | 강사 방식 |
|---|---|
| 환경 | Spring Boot **4.1.2**, Java **25**, `build.gradle.kts`, `application.yaml` |
| 프로파일 | `dev`(H2 파일 `./db_dev`) / `test`(H2 메모리) 분리 |
| SQL 로그 | `format_sql`, `highlight_sql`, 바인딩 파라미터 TRACE 로그 |
| 연관관계 | `@ManyToOne(fetch = LAZY)` 필수, `@OneToMany(mappedBy, cascade = {PERSIST, REMOVE})`, `= new ArrayList<>()`로 초기화 |
| 답변 생성 | `question.addAnswer(content)`: 엔티티 메서드 + cascade PERSIST로 **repository.save 없이** 저장 |
| 샘플 데이터 | `TestInitData`: `ApplicationRunner` + `@Lazy self` + `@Transactional` |
| 테스트 | `@ActiveProfiles("test")` + `@Transactional`(롤백)로 테스트끼리 독립 |
| 예외 | `DataNotFoundException` + `@ResponseStatus(NOT_FOUND)` → 없는 질문은 404 |
| 프론트 | 부트스트랩을 다운로드하지 않고 **cdnjs** 사용, Pretendard 폰트 |
| 커밋 | 작업 하나 = 커밋 하나, 태그 이름 = 커밋 이름(`018-1`, `035-2`) |

### 1-3. 강의 14168: JPA·타임리프 개선 (`p-14168-2`, Post/PostComment 게시판)
| 항목 | 강사 방식 |
|---|---|
| 공통 엔티티 | `BaseEntity`(`@MappedSuperclass`): id, `@CreatedDate`, `@LastModifiedDate` + `@EnableJpaAuditing` |
| 엔티티 설계 | `@Setter` 없이 생성자 + `modify()`, `addComment()`, `deleteComment()` 같은 **의미 있는 메서드** |
| 연관관계 | `orphanRemoval = true`, `equals/hashCode`는 id 기준 |
| 패키지 | `com.back.domain.post.post.{controller,entity,repository,service}` + `com.back.global.*` |
| 폼 | `record WriteForm(...)` + `@ModelAttribute("form")` + `@Valid` + `BindingResult` |
| 에러 메시지 | `"01-title-제목을 입력해주세요."` 형식 → 정렬해서 출력하고, 첫 번째 에러 필드에 focus |
| 타임리프 | `th:object`, `th:field`, `#fields`, 프래그먼트, `layout:decorate`, `layout:title-pattern`, `th:text` vs `th:utext`(XSS) |
| HTTP 메서드 | `GET 폼 / POST 처리`를 같은 URL로, `_method` hidden 필터로 `@PutMapping`·`@DeleteMapping` 사용 |
| global.js | `<a method="DELETE">` 링크를 자동으로 form 전송 + CSRF 토큰 포함 |
| 리다이렉트 | 처리 후 `redirect:`(302) → 새로고침해도 중복 전송 없음 |
| 시큐리티 | 정적 파일을 `/resource/**`에 모아 permitAll, `th:action`으로 CSRF 토큰 자동 처리, h2-console 예외 |
| 트랜잭션 | 컨트롤러 액션에 `@Transactional` / `@Transactional(readOnly = true)` |
| 유효성 검사 | 클라이언트(JS)와 서버 둘 다. **서버 검사는 필수** |

---

## 2. 교재 vs 강사: 적용할 개선 목록

| # | 개선 | 교재 | 적용 방식 | 출처 | 적용 시점 |
|---|---|---|---|---|---|
| A | 최신 환경 | Boot 3.2 / Groovy / properties | Boot 4.1.2 / Java 25 / kts / yaml | 14163 | 001 |
| B | 프로파일 분리 | DB 하나 | dev(파일) / test(메모리), DB 파일은 .gitignore | 14163·14168 | 003 |
| C | SQL 로그 가시화 | show_sql | format + highlight + 바인딩 TRACE | 14163 | 003 |
| D | LAZY 로딩 | ManyToOne EAGER(기본값) | `fetch = LAZY` 명시 | 14163 | 004 |
| E | 컬렉션 초기화 + cascade | `List<Answer> answerList;` | `= new ArrayList<>()`, `{PERSIST, REMOVE}` | 14163 | 004 |
| F | BaseEntity + Auditing | createDate를 직접 대입 | `@CreatedDate`/`@LastModifiedDate` 자동 | 14168 | 004 |
| G | 엔티티 메서드 | `@Setter` + repository.save | `question.addAnswer()`, `modify()` + 더티체킹 | 14163·14168 | 005, 012, 025 |
| H | 테스트 독립성 | 실제 DB에 계속 쌓임 | test 프로파일 + `@Transactional` 롤백 | 14163 | 005 |
| I | 샘플 데이터 자동 생성 | 테스트 코드로 수동 입력 | `ApplicationRunner` + `@Lazy self` | 14163·14168 | 005 |
| J | 404 처리 | 500(No value present) | `DataNotFoundException` → 404 | 14163 | 010 |
| K | 프론트 라이브러리 | 파일 다운로드 | cdnjs + Pretendard | 14163 | 014 |
| L | 폼 DTO | class + Getter/Setter | `record` + `@ModelAttribute("form")` | 14168 | 016 |
| M | 에러 UX | 메시지 나열 | 정렬된 메시지 + 첫 에러 필드 focus 프래그먼트 | 14168 | 016 |
| N | 정적 파일 경로 | `/static/*` | `/static/resource/**` → 시큐리티 설정 단순화 | 14168 | 021 |
| O | CSRF | 수동 hidden input | `th:action` + `<meta name="_csrf">` | 14168 | 021 |
| P | HTTP 메서드 의미 | GET으로 삭제·추천 | PUT(수정) / DELETE(삭제) / POST(추천), `_method` + global.js | 14168 | 025, 026 |
| Q | 마크다운 XSS | `th:utext`로 바로 출력 | utext 위험성 이해 + 원본 HTML 이스케이프 옵션 | 14168(utext) | 028 |
| R | 댓글 삭제 | - | `orphanRemoval` + `equals/hashCode` | 14168 | 031 |

**강의에는 없고 제가 추가로 제안하는 것** (적용 여부는 해당 시점에 결정)
- 권한 실패 응답을 `400` 대신 `403 FORBIDDEN`으로 하고, 권한 체크를 엔티티/서비스 메서드로 이동
- Boot 4에 맞춰 `AntPathRequestMatcher`를 문자열 패턴으로 교체 (이건 선택이 아니라 **필수**)

---

## 3. 확정된 결정 (2026-11-13)

| 항목 | 결정 | 이유 |
|---|---|---|
| 패키지 구조 | **단계적 전환**: 001~005는 교재 방식(`com.mysite.sbb.question`), **006**(교재 2-06)에서 14168 방식(`domain/…/entity·repository·service·controller` + `global/`)으로 이동 | 단순하게 시작하고, 패키지를 나눠야 하는 이유를 직접 겪은 뒤에 전환 |
| URL | **교재 URL 유지 + HTTP 메서드 개선**: 수정 PUT, 삭제 DELETE, 추천 POST (025·026에서 `_method` + `global.js` 도입) | GET으로 상태를 바꾸는 보안 문제만 확실히 고치고, 교재와 대조하기 쉽게 유지 |
| 기능 번호 | `기능번호(3자리)-세부번호(2자리)`. 새 기능 +1, 커밋마다 세부 +1. 교재 절 번호는 쓰지 않음 | 단순함. 실패 커밋, 추가 작업, 도전과제 모두 다음 번호 |
| 태그 | 커밋마다 같은 이름의 태그(`001-02`)를 붙여 push. **push한 커밋은 수정하지 않음**(실수는 다음 커밋으로 바로잡음) | README에 해시 대신 태그 링크를 미리 적을 수 있음 |
| 저장소 | **public** `jhs512/jsb-plus`, 기본 브랜치 `main` | 커밋 이력과 README가 결과물이므로 공유 가능해야 함 |
| 비밀값 | 시크릿·비밀번호는 절대 커밋하지 않음 (gitignore된 설정 파일이나 환경변수 사용, 입력은 사용자가 직접) | public 저장소 |

README 링크 형식: `https://github.com/jhs512/jsb-plus/commit/001-02`

환경 확인 결과: Java 25.0.3 ✅, git 2.56 ✅, gh 로그인(jhs512) ✅, 프로젝트 폴더 비어 있음

---

## 4. 전체 로드맵

> 세부 작업(`-01`, `-02` …)은 **각 기능을 시작할 때** 교재와 강의를 다시 대조해서 확정하고 승인받습니다.
> 아래는 예상 분할입니다.

### 미션 1: 교재 2장, 기본 기능
| 번호 | 기능 | 교재 | 핵심 학습 포인트 | 예상 세부 작업 |
|---|---|---|---|---|
| 000 | 저장소 준비 | - | git, 태그, README 작업내역 | 01 git init·.gitignore·README / 02 GitHub 원격 연결·push |
| 001 | 프로젝트 생성 | 1장 | Initializr, kts, 메인 클래스 | 01 Initializr 생성 / 02 실행 확인 |
| 002 | 간단한 웹 프로그램 | 2-02 | DispatcherServlet, `@ResponseBody`, 404/500 | 01 void 액션(500 확인) / 02 `@ResponseBody`(200) |
| 003 | DB 설정 | 2-03 | H2, 프로파일, SQL 로그 | 01 H2·콘솔 / 02 dev·test 분리 / 03 JPA·SQL 로그 |
| 004 | 엔티티 | 2-04 | ManyToOne/OneToMany, mappedBy, LAZY, cascade | 01 Question / 02 Answer·관계 / 03 mappedBy 유무 테이블 비교 / 04 BaseEntity·Auditing |
| 005 | 리포지터리·테스트 | 2-05 | 쿼리 메서드, `@Transactional` 롤백, 더티체킹, flush | 01 Repository·TestInitData / 02 findAll·findById / 03 findBy 명명규칙 / 04 수정·삭제(롤백) / 05 답변 생성(save vs addAnswer) / 06 LAZY와 트랜잭션 |
| 006 | 도메인별 분류 | 2-06 | 패키지 구조 | 01 패키지 이동 |
| 007 | 질문 목록 | 2-07 | 타임리프, Model, th:each | 01 문자열 응답 / 02 템플릿 도입 / 03 목록 출력 / 04 테이블 |
| 008 | 루트 URL | 2-08 | redirect | 01 |
| 009 | 서비스 | 2-09 | 계층 분리, 생성자 주입 | 01 |
| 010 | 상세 페이지 | 2-10 | `@PathVariable`, `@{}`·`\|\|`, 커스텀 예외 | 01 상세 / 02 목록 링크 / 03 404 |
| 011 | URL 프리픽스 | 2-11 | `@RequestMapping` | 01 |
| 012 | 답변 기능 | 2-12 | POST, PRG 패턴, 엔티티 메서드 | 01 폼 / 02 저장(addAnswer) / 03 답변 출력 |
| 013 | 웹 페이지 디자인 | 2-13 | 정적 리소스 | 01 |
| 014 | 부트스트랩 | 2-14 | cdnjs, 폰트 | 01 |
| 015 | 표준 HTML·레이아웃 | 2-15 | layout dialect, title-pattern | 01 layout / 02 title-pattern |
| 016 | 질문 등록·폼 검증 | 2-16 | record 폼, `@Valid`, BindingResult, `#fields` | 01 등록 폼 / 02 저장 / 03 검증 / 04 에러 프래그먼트·정렬 / 05 focus / 06 답변 폼 검증 |

### 미션 1: 교재 3장, 서비스 기능
| 번호 | 기능 | 교재 | 핵심 학습 포인트 |
|---|---|---|---|
| 017 | 내비게이션 바 | 3-01 | 프래그먼트 재사용 |
| 018 | 페이징 | 3-02 | `Pageable`, `Page`, 대량 데이터 테스트 |
| 019 | 게시물 번호 | 3-03 | 역순 번호 계산 |
| 020 | 답변 개수 | 3-04 | `#lists.size`, N+1 맛보기 |
| 021 | 스프링 시큐리티 | 3-05 | 필터 체인, 기본 동작(전부 차단·CSRF), `/resource/**`, `th:action` |
| 022 | 회원가입 | 3-06 | `SiteUser`, BCrypt, 중복 처리 |
| 023 | 로그인·로그아웃 | 3-07 | `UserDetailsService`, 세션 쿠키 |
| 024 | 글쓴이 | 3-08 | `Principal`, `@PreAuthorize` |
| 025 | 수정·삭제 | 3-09 | `_method`, PUT/DELETE, global.js, modifyDate 자동, 권한 체크 |
| 026 | 추천 | 3-10 | `@ManyToMany`, Set, POST |
| 027 | 앵커 | 3-11 | redirect + `#answer_id` |
| 028 | 마크다운 | 3-12 | commonmark, `th:utext`와 XSS |
| 029 | 검색 | 3-13 | Specification vs `@Query`, join + distinct |

(4장 배포는 범위에서 제외합니다. 원하면 마지막에 따로 진행합니다.)

### 미션 2: 도전과제 (교재 3-14)
| 번호 | 기능 | 학습 포인트 / 주의 |
|---|---|---|
| 030 | 답변 페이징과 정렬 | 최신순 / 추천순 정렬, 상세 화면 안에서 페이징 |
| 031 | 댓글 (질문·답변) | 14168 PostComment 패턴: orphanRemoval, equals/hashCode, 클라이언트 검증 |
| 032 | 카테고리 | 질문게시판 / 자유게시판, 연관관계 추가 |
| 033 | 비밀번호 찾기·변경 | 메일 발송은 실제 SMTP가 필요 → 개발 환경에서 검증할 대체 방법 결정 |
| 034 | 프로필 | 내가 쓴 질문·답변·댓글 |
| 035 | 최근 답변·댓글 | 정렬 쿼리 |
| 036 | 조회 수 | 새로고침마다 증가하는 문제 / 동시성 맛보기 |
| 037 | 소셜 로그인 | OAuth2 Client. **클라이언트 ID·시크릿 발급과 입력은 직접 하셔야 합니다** |
| 038 | 마크다운 에디터 | SimpleMDE(또는 후속 EasyMDE)를 cdnjs로 적용 |

---

## 5. 작업 하나의 사이클 (모든 세부 작업에 동일하게 적용)

```
① 설명    교재는 이렇게, 강사는 이렇게. 차이와 이유를 설명
② 승인    "이번 작업: 0XX-0Y ○○○, 바꿀 파일: …" → 사용자 승인
③ 구현    작은 변경 하나
④ 설명    바뀐 코드를 줄 단위로 설명
⑤ 검증    테스트 실행(./gradlew test) + bootRun 후 브라우저 화면, SQL 로그, H2 콘솔을 직접 보여줌
⑥ 이해 확인  확인 질문 1~2개 → 사용자가 자기 말로 설명
⑦ 기록    README 작업내역 + 커밋(작업 내용/이유/검증) + 같은 이름의 태그 → push
⑧ 승인 후 다음 작업
```

### 커밋 메시지 템플릿
```
004-02 엔티티, Answer 엔티티와 ManyToOne 관계 추가

작업 내용:
- Answer 엔티티 추가, @ManyToOne(fetch = LAZY) question

작업 이유:
- 답변은 반드시 하나의 질문에 속함 (교재 2-04)
- ManyToOne 기본값 EAGER 대신 LAZY 사용 (14163 25강)

검증:
- bootRun 후 H2 콘솔에서 ANSWER.QUESTION_ID 외래키 확인
```

### README 작업내역 템플릿
```markdown
### 004-02 엔티티, Answer 엔티티와 ManyToOne 관계 추가
- [링크](https://github.com/jhs512/jsb-plus/commit/004-02)
- 작업: Answer 엔티티와 ManyToOne(LAZY) 관계 추가
- 이유: 답변-질문 N:1 관계 매핑, 불필요한 즉시 로딩 방지
- 결과: answer 테이블에 question_id FK 생성 확인
```

---

## 6. 진행 중 지킬 원칙
- 한 번에 파일 1~3개, 개념 1개만 다룹니다.
- 강사의 **"일부러 실패하는 커밋"** 방식(예: `@Transactional`이 없어서 실패 → 붙여서 성공)은 이해에 효과가 크므로 적극적으로 따라 합니다.
- 교재와 다르게 만든 부분은 README에 이유를 남깁니다.
- 막히거나 이해가 안 되면 진도를 멈추고 개념부터 설명합니다.
