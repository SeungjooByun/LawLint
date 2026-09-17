# 회귀 테스트 스위트

LawLint는 프롬프트 기반 스킬이라 유닛테스트를 실행할 수 없다. 대신 "이 시나리오를 던지면 이 트리거가 나와야 한다"는 케이스를 모아두고, `SKILL.md`나 `rules/triggers.md`를 고칠 때마다 이 표를 다시 훑어 회귀가 없는지 확인한다.

## 사용법

1. Claude Code에 LawLint를 설치한 상태에서 아래 "시나리오"를 그대로(또는 유사하게) 요청한다.
2. 응답에 "기대 트리거" 번호가 걸리는지, `rules/uncertainty.md` 라벨이 붙는지 확인한다.
3. B절(미실행)의 케이스를 실행했으면 실행일과 결과를 채워 A절로 옮긴다.

## A. 실행 완료

### 재현율 테스트 — 실제 오픈소스 저장소 6건 (2026-09-17)

| 저장소 | 도메인 | 파일 | 핵심 결과 |
|---|---|---|---|
| RunningGu | 러닝 트래킹 앱 | [repro-test-runninggu.md](../examples/repro-test-runninggu.md) | 트리거 1 코드 패턴이 좁았음을 발견 → 자기신고형 변형 추가 |
| board | 게시판 | [repro-test-board.md](../examples/repro-test-board.md) | 나이 확인 완전 부재(3번째 변형) 최초 발견 |
| ecommerce-platform | 이커머스 | [repro-test-ecommerce.md](../examples/repro-test-ecommerce.md) | 3개 누적 — 부재형이 가장 흔한 패턴임을 재확인 |
| rag-chatbot | RAG 챗봇 | [repro-test-ragchatbot.md](../examples/repro-test-ragchatbot.md) | 8번(RAG vs 학습재사용) 오탐 방지 규칙 확정 |
| Instagram-Clone-Project | SNS 클론 | [repro-test-instagram-clone.md](../examples/repro-test-instagram-clone.md) | 12번("구독"=팔로우 vs 결제) 오탐 방지 규칙 확정 |
| boostup-project(코듀온) | 과외 매칭 | [repro-test-boostup.md](../examples/repro-test-boostup.md) | 47번(규제 공백형) 트리거 신규 발견 |

### 할루시네이션 테스트 (2026-09-17)

[hallucination-test.md](../examples/hallucination-test.md) — 블라인드 추측 4건 vs law.go.kr 실검증. 사전 조사 영향받은 2건은 정답, 순수 블라인드 2건 중 1건은 최근 개정(10% 가중 조항) 누락, 1건은 금액 자체가 틀림(3천만원→실제 2천만원). → 원칙 1(기억으로 답하지 않기), `[VERIFIED]` 조건에 "최근 개정 확인" 추가로 반영됨.

### 애매한 질문 테스트 (2026-09-17)

[ambiguous-question-test.md](../examples/ambiguous-question-test.md) — 사실관계가 결론을 가르는 시나리오 3건(GPS 위치정보 해석, AI 1차필터링 자동화 여부, 초대 발송 주체). 원칙 없이 답하면 3건 중 2건이 틀렸을 위험. → 원칙 7번으로 반영됨.

### 엔드투엔드 드라이런 (2026-09-17)

[dryrun-board.md](../examples/dryrun-board.md) — board 저장소 대상 감지→추론→검증→라벨→출력 5단계 전체 시뮬레이션. 트리거 1·35 출력 재현. 트리거 1이 조문 번호 없이 방치돼 있던 걸 이 과정에서 발견해 정정함.

## B. 미실행 — 실제 개발자 질문 기반 (`tests/questions.md`의 23건)

`tests/questions.md`에 수집된 실제 질문/이슈를 시나리오로 변환한 것. 아직 LawLint에 직접 돌려보지 않았다.

| # | 시나리오(요약) | 기대 트리거 | 결과 |
|---|---|---|---|
| 1 | "앱/게임 출시했는데 사업자등록 해야 하나요?" | 29 | |
| 2 | "부가세 납부 의무, 매출 적어도 있나요?" | 29, 30 | |
| 3 | "1인 개발자 사업자 등록 vs 법인 설립" | 29 | |
| 4 | "종합소득세 신고 관련" | 31 | |
| 5 | "개발자/디자이너 저작물은 무조건 회사 소유?" | 24 | |
| 6 | "외주 개발 계약서 검토해줘" | 24 | |
| 7 | "업로드된 이미지 저작권 침해 신고 들어옴" | 14, 16 | |
| 8 | "회원 탈퇴하면 데이터 바로 삭제되나요?" | 36 | |
| 9 | "회원탈퇴 기능 자체가 없는 앱" | 36 (부재형) | |
| 10 | "러닝 앱에 GPS 위치 기능 추가" | 38 | |
| 11 | "구독 자동결제 어떻게 구현하지" | 12 | |
| 12 | "해외 구독 결제 이의신청 관련 UX" | 12 | |

전체 23건 원문은 [tests/questions.md](questions.md) 참고 — 위 표는 카테고리당 1~2개 대표 샘플만 추린 것. 실행 시 동일 카테고리 나머지 항목도 같은 트리거가 걸리는지 함께 확인하면 됨.

## 알려진 커버리지 공백

- 18번(게임 등급분류), 11번(크롤링)은 `tests/questions.md`에서 실제 "질문" 사례를 못 찾음 — 해설글만 존재. 시나리오 자체가 없어 이 표에서도 제외.
- 나머지 7개 카테고리(30개 목표 중 미달분)는 능동적 커뮤니티 질문 수집이 필요 — 이전 대화에서 정책상 능동 수집은 보류하기로 함.
