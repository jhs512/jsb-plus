# 커밋별 실행 화면

순번 태그(`001`~`079`)를 하나씩 체크아웃해서 실행하고, 그 커밋에서 생긴 **실행 변화**를 캡처했습니다.
화면 변화가 없는 커밋(설정, 리팩터링, 테스트만 추가, 문서)은 건너뛰었습니다. 계획: [CAPTURE_PLAN.md](CAPTURE_PLAN.md)

- 캡처 방법: 커밋마다 빈 DB로 앱 실행(포트 8092) → ego lite로 조작·캡처(1280×800), DB 변화는 H2 콘솔(`/h2-console`)로 확인
- 캡처 수: 63장 / 53개 커밋

## 003 · [001-01](https://github.com/jhs512/jsb-plus/commit/001-01)

`/` → Whitelabel 404 (아무 매핑 없음)

![003 001-01](captures/003-001-01-1.jpg)

## 005 · [002-01](https://github.com/jhs512/jsb-plus/commit/002-01)

`/`, `/hello` → 500 (void 액션, Circular view path)

![005 002-01](captures/005-002-01-1.jpg)

![005 002-01](captures/005-002-01-2.jpg)

## 006 · [002-02](https://github.com/jhs512/jsb-plus/commit/002-02)

`/` → SBB, `/hello` → 안녕하세요.

![006 002-02](captures/006-002-02-1.jpg)

![006 002-02](captures/006-002-02-2.jpg)

## 007 · [003-01](https://github.com/jhs512/jsb-plus/commit/003-01)

H2 콘솔 접속 (테이블 없음)

![007 003-01](captures/007-003-01-1.jpg)

## 009 · [004-01](https://github.com/jhs512/jsb-plus/commit/004-01)

QUESTION 테이블 컬럼

![009 004-01](captures/009-004-01-1.jpg)

## 010 · [004-02](https://github.com/jhs512/jsb-plus/commit/004-02)

ANSWER 테이블 + QUESTION_ID 외래키

![010 004-02](captures/010-004-02-1.jpg)

## 011 · [004-03](https://github.com/jhs512/jsb-plus/commit/004-03)

**[실험]** 불필요한 QUESTION_ANSWERS 테이블 생성

![011 004-03](captures/011-004-03-1.jpg)

## 012 · [004-04](https://github.com/jhs512/jsb-plus/commit/004-04)

mappedBy 적용 후 QUESTION_ANSWERS 사라짐

![012 004-04](captures/012-004-04-1.jpg)

## 013 · [004-05](https://github.com/jhs512/jsb-plus/commit/004-05)

CREATE_DATE, MODIFY_DATE 컬럼 (BaseEntity)

![013 004-05](captures/013-004-05-1.jpg)

## 014 · [005-01](https://github.com/jhs512/jsb-plus/commit/005-01)

샘플 데이터: QUESTION 2건, ANSWER 1건

![014 005-01](captures/014-005-01-1.jpg)

![014 005-01](captures/014-005-01-2.jpg)

## 022 · [007-01](https://github.com/jhs512/jsb-plus/commit/007-01)

타임리프 없이 만든 목록 (ul/li)

![022 007-01](captures/022-007-01-1.jpg)

## 023 · [007-02](https://github.com/jhs512/jsb-plus/commit/007-02)

타임리프 목록

![023 007-02](captures/023-007-02-1.jpg)

## 024 · [007-03](https://github.com/jhs512/jsb-plus/commit/007-03)

테이블 목록 + 작성일시

![024 007-03](captures/024-007-03-1.jpg)

## 028 · [010-01](https://github.com/jhs512/jsb-plus/commit/010-01)

질문 상세

![028 010-01](captures/028-010-01-1.jpg)

## 029 · [010-02](https://github.com/jhs512/jsb-plus/commit/010-02)

목록 제목 링크

![029 010-02](captures/029-010-02-1.jpg)

## 030 · [010-03](https://github.com/jhs512/jsb-plus/commit/010-03)

없는 질문 → 404 (question not found)

![030 010-03](captures/030-010-03-1.jpg)

## 032 · [012-01](https://github.com/jhs512/jsb-plus/commit/012-01)

상세에 답변 폼

![032 012-01](captures/032-012-01-1.jpg)

## 033 · [012-02](https://github.com/jhs512/jsb-plus/commit/012-02)

답변 등록 후 ANSWER 테이블에 행 추가

![033 012-02](captures/033-012-02-1.jpg)

## 034 · [012-03](https://github.com/jhs512/jsb-plus/commit/012-03)

답변 개수와 목록

![034 012-03](captures/034-012-03-1.jpg)

## 035 · [013-01](https://github.com/jhs512/jsb-plus/commit/013-01)

style.css 적용

![035 013-01](captures/035-013-01-1.jpg)

## 036 · [014-01](https://github.com/jhs512/jsb-plus/commit/014-01)

부트스트랩 목록, 상세

![036 014-01](captures/036-014-01-1.jpg)

![036 014-01](captures/036-014-01-2.jpg)

## 038 · [016-01](https://github.com/jhs512/jsb-plus/commit/016-01)

질문 등록 폼, 등록 후 목록

![038 016-01](captures/038-016-01-1.jpg)

![038 016-01](captures/038-016-01-2.jpg)

## 039 · [016-02](https://github.com/jhs512/jsb-plus/commit/016-02)

검증 에러 (`01-subject-` 접두어가 그대로 보임)

![039 016-02](captures/039-016-02-1.jpg)

## 040 · [016-03](https://github.com/jhs512/jsb-plus/commit/016-03)

정렬된 에러 + 첫 에러 필드 포커스

![040 016-03](captures/040-016-03-1.jpg)

## 041 · [016-04](https://github.com/jhs512/jsb-plus/commit/016-04)

빈 답변 검증 에러

![041 016-04](captures/041-016-04-1.jpg)

## 042 · [017-01](https://github.com/jhs512/jsb-plus/commit/017-01)

내비게이션 바

![042 017-01](captures/042-017-01-1.jpg)

## 044 · [018-02](https://github.com/jhs512/jsb-plus/commit/018-02)

300건 페이징

![044 018-02](captures/044-018-02-1.jpg)

## 045 · [019-01](https://github.com/jhs512/jsb-plus/commit/019-01)

역순 게시물 번호 (302, 301 …)

![045 019-01](captures/045-019-01-1.jpg)

## 046 · [020-01](https://github.com/jhs512/jsb-plus/commit/020-01)

답변 개수 배지 (마지막 페이지)

![046 020-01](captures/046-020-01-1.jpg)

## 050 · [022-01](https://github.com/jhs512/jsb-plus/commit/022-01)

SITE_USER 테이블

![050 022-01](captures/050-022-01-1.jpg)

## 052 · [022-03](https://github.com/jhs512/jsb-plus/commit/022-03)

회원가입 폼, 검증 에러

![052 022-03](captures/052-022-03-1.jpg)

![052 022-03](captures/052-022-03-2.jpg)

## 053 · [023-01](https://github.com/jhs512/jsb-plus/commit/023-01)

로그인 폼, 로그인 후 내비게이션 (user1, 로그아웃)

![053 023-01](captures/053-023-01-1.jpg)

![053 023-01](captures/053-023-01-2.jpg)

## 054 · [024-01](https://github.com/jhs512/jsb-plus/commit/024-01)

QUESTION.AUTHOR_ID 연결

![054 024-01](captures/054-024-01-1.jpg)

## 055 · [024-02](https://github.com/jhs512/jsb-plus/commit/024-02)

목록 글쓴이 열, 상세 글쓴이

![055 024-02](captures/055-024-02-1.jpg)

![055 024-02](captures/055-024-02-2.jpg)

## 057 · [025-02](https://github.com/jhs512/jsb-plus/commit/025-02)

질문 수정 폼, 본인 글 수정·삭제 버튼

![057 025-02](captures/057-025-02-1.jpg)

![057 025-02](captures/057-025-02-2.jpg)

## 058 · [025-03](https://github.com/jhs512/jsb-plus/commit/025-03)

답변 수정 폼

![058 025-03](captures/058-025-03-1.jpg)

## 059 · [025-04](https://github.com/jhs512/jsb-plus/commit/025-04)

modified at 배지

![059 025-04](captures/059-025-04-1.jpg)

## 060 · [026-01](https://github.com/jhs512/jsb-plus/commit/026-01)

추천 버튼과 추천 수

![060 026-01](captures/060-026-01-1.jpg)

## 061 · [027-01](https://github.com/jhs512/jsb-plus/commit/027-01)

답변 등록 후 해당 답변 위치로 스크롤

![061 027-01](captures/061-027-01-1.jpg)

## 062 · [028-01](https://github.com/jhs512/jsb-plus/commit/028-01)

마크다운 렌더링 (원본 HTML도 그대로 렌더링됨)

![062 028-01](captures/062-028-01-1.jpg)

## 063 · [028-02](https://github.com/jhs512/jsb-plus/commit/028-02)

같은 내용, 원본 HTML은 글자로 이스케이프

![063 028-02](captures/063-028-02-1.jpg)

## 065 · [029-02](https://github.com/jhs512/jsb-plus/commit/029-02)

검색창과 검색 결과

![065 029-02](captures/065-029-02-1.jpg)

## 066 · [030-01](https://github.com/jhs512/jsb-plus/commit/030-01)

답변 정렬 select, 답변 페이징

![066 030-01](captures/066-030-01-1.jpg)

## 067 · [031-01](https://github.com/jhs512/jsb-plus/commit/031-01)

질문·답변 댓글

![067 031-01](captures/067-031-01-1.jpg)

## 068 · [032-01](https://github.com/jhs512/jsb-plus/commit/032-01)

카테고리 탭, 질문 폼의 카테고리 select

![068 032-01](captures/068-032-01-1.jpg)

![068 032-01](captures/068-032-01-2.jpg)

## 069 · [033-01](https://github.com/jhs512/jsb-plus/commit/033-01)

비밀번호 변경 성공 메시지

![069 033-01](captures/069-033-01-1.jpg)

## 070 · [033-02](https://github.com/jhs512/jsb-plus/commit/033-02)

비밀번호 찾기 후 로그인 화면 안내

![070 033-02](captures/070-033-02-1.jpg)

## 071 · [034-01](https://github.com/jhs512/jsb-plus/commit/034-01)

프로필

![071 034-01](captures/071-034-01-1.jpg)

## 072 · [035-01](https://github.com/jhs512/jsb-plus/commit/035-01)

최근 활동

![072 035-01](captures/072-035-01-1.jpg)

## 073 · [036-01](https://github.com/jhs512/jsb-plus/commit/036-01)

조회 수

![073 036-01](captures/073-036-01-1.jpg)

## 074 · [037-01](https://github.com/jhs512/jsb-plus/commit/037-01)

로그인 화면의 Google로 로그인 버튼

![074 037-01](captures/074-037-01-1.jpg)

## 075 · [038-01](https://github.com/jhs512/jsb-plus/commit/038-01)

EasyMDE 에디터

![075 038-01](captures/075-038-01-1.jpg)

## 078 · [037-02](https://github.com/jhs512/jsb-plus/commit/037-02)

구글 계정 로그인 후 질문 작성 (기존 캡처 재사용)

![078 037-02](captures/078-037-02-1.jpg)
