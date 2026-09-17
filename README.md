# LawLint

> **LawLint는 "물어보면 답하는 법률 비서"가 아니라, 개발자가 질문할 생각조차 하지 못한 순간에 코드를 먼저 보고 끼어드는 "AI 기본법 특화 사전 감지 도구"다.**

법순이(beopsuny)가 물어보면 답하는 사내 변호사라면, LawLint는 AI 기능을 코드로 짜는 순간 어깨 너머로 보고 있는 검토자다.

## 범위

한국 **AI 관련 법률** 1개 도메인으로 좁힌다. 법률 지식을 암기해 답하지 않고, 코드 패턴이 트리거를 건드리면 beopsuny / korean-law-mcp 같은 실시간 공식 소스 조회 엔진을 강제로 호출해 근거를 붙인다. 확인 안 된 내용은 `[UNVERIFIED]`로 표시한다.

## 구조

- `rules/` — 트리거 목록, 법률 판단 규칙, 불확실성 처리 규칙
- `workflows/` — 트리거 감지 후 검증 엔진 호출 절차
- `templates/` — 리뷰 결과 출력 포맷
- `examples/` — 실제 트리거 재현 사례
- `tests/` — 트리거 재현율 테스트 케이스

## 설치

[INSTALL.md](INSTALL.md) 참고 — 5분 안에 첫 검토를 받을 수 있다.

## 현재 상태

v0.1. `SKILL.md`가 동작하며, 트리거 47개(`rules/triggers.md`), law.go.kr 실시간 검증([workflows/trigger-to-verification.md](workflows/trigger-to-verification.md)), 재현율·할루시네이션·애매한 질문·엔드투엔드 드라이런 테스트를 거쳤다(`examples/` 참고). 47개 트리거 중 상당수가 여전히 `[UNVERIFIED]` 상태이며, 실제로 트리거가 걸릴 때마다 그 순간 다시 검증하는 구조다.

자세한 배경과 4주 계획은 [LawLint_계획_최종.md](LawLint_계획_최종.md) 참고.
