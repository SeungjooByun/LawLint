# LawLint 설치 가이드

목표: 5분 안에 첫 검토를 받는다. korean-law-mcp나 beopsuny 같은 추가 설치 없이, LawLint 폴더 하나만 있으면 된다([workflows/trigger-to-verification.md](workflows/trigger-to-verification.md)에서 확정한 대로 law.go.kr을 기본 검증 경로로 쓰기 때문).

## 0. 전제조건

- Claude Code가 설치돼 있어야 한다 (`claude` 명령어 사용 가능).
- 이 저장소(LawLint 폴더) 전체를 받아둔다 — git clone(공개 후) 또는 폴더째 복사.

## 1. 설치 위치 선택

LawLint는 Claude Code의 "스킬(Skill)"이다. 스킬은 특정 폴더에 `SKILL.md`가 있으면 Claude Code가 자동으로 인식한다. 어디에 두느냐에 따라 적용 범위가 다르다.

| 위치 | 적용 범위 | 언제 쓰나 |
|---|---|---|
| `~/.claude/skills/lawlint/` | **모든 프로젝트에서 항상 켜짐** | 여러 사이드 프로젝트를 오가며 개발하는 경우(추천) |
| `<프로젝트>/.claude/skills/lawlint/` | 그 프로젝트에서만 켜짐 | 팀 프로젝트라 다른 사람에게는 아직 영향 주고 싶지 않은 경우 |

**대부분은 전역 설치(`~/.claude/skills/lawlint/`)를 추천한다** — "질문할 생각도 못 한 순간에 먼저 짚어준다"는 LawLint의 목적상, 어느 프로젝트를 열든 항상 켜져 있어야 의미가 있다.

## 2. 설치

### Windows (PowerShell)

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\skills"
Copy-Item -Path "C:\Users\user\LawLint" -Destination "$env:USERPROFILE\.claude\skills\lawlint" -Recurse
```

### macOS / Linux

```bash
mkdir -p ~/.claude/skills
cp -r /path/to/LawLint ~/.claude/skills/lawlint
```

**주의**: 폴더 이름을 반드시 `lawlint`로 맞춘다(SKILL.md의 `name: lawlint`와 일치해야 함). `rules/`, `workflows/`, `templates/`, `examples/` 전부 SKILL.md가 상대경로로 참조하므로 폴더 전체를 통째로 복사해야 한다 — `SKILL.md` 파일 하나만 복사하면 동작하지 않는다.

## 3. 설치 확인

아무 프로젝트나 열고 Claude Code에게 물어본다:

```
사용 가능한 스킬 목록 보여줘
```

또는 아무 프로젝트 대화에서

```
/lawlint
```

를 쳐서 자동완성에 뜨는지 확인한다. 목록에 `lawlint`가 보이면 설치 완료.

## 4. 첫 검토 (5분 테스트)

새 프로젝트든 기존 프로젝트든, 다음 중 하나를 Claude Code에게 시켜본다.

```
회원가입 API 만들어줘. 이메일, 비밀번호, 닉네임 받고 가입 처리하는 거야.
```

이 요청 자체에는 아무 법적 문제가 없어 보이지만, LawLint가 정상적으로 작동한다면:
- 나이 확인 로직이 없다는 점(트리거 1)
- 개인정보처리방침이 없다는 점(트리거 35)

을 코드 작성 후(또는 작성 중) 먼저 짚어줘야 한다. 실제 예시는 [examples/dryrun-board.md](examples/dryrun-board.md)에서 확인할 수 있다.

**아무 반응이 없다면** → 6절 "문제 해결"로.

## 5. 알아둘 것

- LawLint는 변호사가 아니다. "확인이 필요한 지점"을 짚어줄 뿐, 최종 판단은 직접 하거나 전문가 자문을 받아야 한다 — [SKILL.md](SKILL.md) "하지 않는 것" 참고.
- 트리거가 걸릴 때마다 law.go.kr에 실시간으로 접속해서 원문을 확인한다 — 네트워크 연결이 필요하고, 매번 몇 초 정도 걸릴 수 있다.
- korean-law-mcp, beopsuny, 법망 API는 전부 선택 사항이다. 설치 안 해도 기본 기능(law.go.kr 직접 조회)은 그대로 작동한다.
- 현재 v0.1이라 `rules/triggers.md`의 47개 항목 중 상당수가 `[UNVERIFIED]` 상태다 — 실제로 트리거가 걸릴 때 다시 검증하는 구조이므로 목록에 적힌 조문을 그대로 믿지 않는다.

## 6. 문제 해결

**스킬 목록에 lawlint가 안 보임** → 폴더 이름이 `lawlint`인지, `SKILL.md`가 그 폴더 바로 아래(하위 폴더 아님)에 있는지 확인. Claude Code를 재시작해야 새 스킬을 인식하는 경우도 있다.

**스킬은 보이는데 코드를 봐도 반응이 없음** → 요청 내용이 `SKILL.md`의 `description`에 있는 키워드(개인정보 수집, 결제, AI API, 업로드 등)와 겹치는지 확인. 완전히 무관한 요청(예: "이 함수 리팩터링해줘")에는 반응하지 않는 게 정상.

**law.go.kr 조회가 안 됨/느림** → `workflows/trigger-to-verification.md`에 정리된 대로 law.go.kr 자체가 자바스크립트 렌더링 사이트라 Browser 도구가 필요하다. Claude Code의 Browser 도구가 이 환경에서 사용 가능한지 확인.

## 7. 제거

```bash
rm -rf ~/.claude/skills/lawlint
```
(Windows: `Remove-Item -Recurse -Force "$env:USERPROFILE\.claude\skills\lawlint"`)
