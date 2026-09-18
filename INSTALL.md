# 설치 가이드

목표는 5분 안에 첫 리뷰를 받아보는 것. korean-law-mcp나 beopsuny 같은 걸 따로 설치할 필요 없이 이 폴더 하나만 있으면 된다 — law.go.kr을 직접 조회하는 방식이라([workflows/trigger-to-verification.md](workflows/trigger-to-verification.md) 참고), 다른 도구에 의존하지 않는다.

## 준비물

Claude Code가 설치돼 있어야 하고(`claude` 명령어가 먹혀야 함), 이 저장소를 통째로 받아둬야 한다.

```bash
git clone https://github.com/SeungjooByun/LawLint.git
```

## 어디에 둘지 정하기

LawLint는 Claude Code의 "스킬"이다. `SKILL.md`가 있는 폴더를 Claude Code가 자동으로 인식하는데, 어디에 두느냐에 따라 적용 범위가 달라진다.

전역으로 두면(`~/.claude/skills/lawlint/`) 어떤 프로젝트를 열든 항상 켜져 있고, 프로젝트 안에 두면(`<프로젝트>/.claude/skills/lawlint/`) 그 프로젝트에서만 동작한다. 특별한 이유가 없으면 전역 설치를 권한다 — 질문할 생각도 못 한 순간에 먼저 짚어주는 게 LawLint의 존재 이유라서, 매번 켜져 있어야 의미가 있다.

## 설치

클론받은 폴더를 스킬 디렉터리로 복사하면 된다. `git clone`으로 받았다면 현재 디렉터리에 `LawLint` 폴더가 생겼을 것이다.

**Windows (PowerShell)**

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\skills"
Copy-Item -Path ".\LawLint" -Destination "$env:USERPROFILE\.claude\skills\lawlint" -Recurse
```

**macOS / Linux**

```bash
mkdir -p ~/.claude/skills
cp -r ./LawLint ~/.claude/skills/lawlint
```

폴더 이름은 반드시 `lawlint`로 맞춰야 한다(`SKILL.md`의 `name: lawlint`와 일치해야 인식됨). `rules/`, `workflows/`, `templates/`, `examples/`는 전부 `SKILL.md`가 상대경로로 참조하고 있어서, `SKILL.md` 하나만 복사하면 동작하지 않는다 — 폴더째로 복사해야 한다.

## 잘 설치됐는지 확인

아무 프로젝트나 열고 Claude Code에게 "사용 가능한 스킬 목록 보여줘"라고 물어보거나, 대화창에 `/lawlint`를 쳐서 자동완성에 뜨는지 보면 된다. 목록에 `lawlint`가 보이면 끝.

## 5분 테스트

다음처럼 평범한 기능 요청을 던져본다.

```
회원가입 API 만들어줘. 이메일, 비밀번호, 닉네임 받고 가입 처리하는 거야.
```

요청 자체는 아무 문제 없어 보이지만, LawLint가 제대로 작동한다면 나이 확인 로직이 빠졌다는 점(트리거 1)과 개인정보처리방침이 없다는 점(트리거 35)을 코드를 작성하면서 먼저 짚어줘야 한다. 실제로 이런 식으로 나온 예시는 [examples/dryrun-board.md](examples/dryrun-board.md)에서 볼 수 있다.

반응이 없으면 아래 "문제 해결"로.

## 알아둘 것

LawLint는 변호사가 아니다. 확인이 필요한 지점을 짚어줄 뿐이고, 최종 판단은 직접 하거나 전문가 자문을 받아야 한다 — 자세한 내용은 [DISCLAIMER.md](DISCLAIMER.md)에 있다.

트리거가 걸릴 때마다 law.go.kr에 실시간으로 접속해서 원문을 확인하기 때문에 네트워크 연결이 필요하고, 매번 몇 초 정도 걸릴 수 있다. korean-law-mcp나 beopsuny, 법망 API는 전부 선택 사항이라 설치하지 않아도 기본 기능은 그대로 작동한다.

아직 v0.1이라 `rules/triggers.md`의 47개 항목 중 일부는 `[UNVERIFIED]` 상태로 남아 있다. 실제로 트리거가 걸리는 순간 다시 검증하는 구조라서, 목록에 적힌 조문을 그대로 믿지 않는 게 맞다.

## 문제 해결

스킬 목록에 `lawlint`가 안 보이면 폴더 이름이 정확히 `lawlint`인지, `SKILL.md`가 그 폴더 바로 아래에 있는지(하위 폴더 아님) 확인해보고, Claude Code를 재시작해본다.

스킬은 보이는데 코드를 봐도 반응이 없으면, 요청 내용이 `SKILL.md`의 `description`에 있는 키워드(개인정보 수집, 결제, AI API, 업로드 등)와 겹치는지 본다. "이 함수 리팩터링해줘"처럼 법적 리스크와 무관한 요청엔 반응하지 않는 게 정상이다.

law.go.kr 조회가 안 되거나 느리면, law.go.kr이 자바스크립트로 렌더링되는 사이트라 Claude Code의 Browser 도구가 필요하다는 점을 확인해본다(자세한 내용은 [workflows/trigger-to-verification.md](workflows/trigger-to-verification.md)).

## Codex CLI에서도 될까

Claude Code용으로 만들었지만, 2026년 기준 OpenAI Codex CLI도 같은 형식의 `SKILL.md`(`name`/`description` YAML frontmatter)와 description 기반 자동 활성화를 지원한다고 한다. 그대로 옮겨갈 가능성은 있어 보이지만, 설치 경로가 다르고(`~/.agents/skills/lawlint/`), 이 환경엔 Codex CLI가 없어서 직접 확인은 못 해봤다. 공개된 문서를 조사한 수준이니 실제로 써보기 전엔 참고만 할 것.

## 업데이트

GitHub에 새 커밋이 올라가도 이미 설치한 사람한테 자동으로 반영되지 않는다. 설치 과정 자체가 클론한 폴더를 복사해서 넣는 방식이라, `~/.claude/skills/lawlint`는 그 시점의 스냅샷일 뿐이고 원본 저장소와 연결되어 있지 않다.

업데이트하려면 처음 클론했던 폴더로 가서 최신 내용을 받은 다음, 설치 폴더에 덮어쓰면 된다.

```bash
cd LawLint
git pull
```

**Windows (PowerShell)**

```powershell
Copy-Item -Path ".\LawLint" -Destination "$env:USERPROFILE\.claude\skills\lawlint" -Recurse -Force
```

**macOS / Linux**

```bash
cp -r ./LawLint/. ~/.claude/skills/lawlint/
```

처음 클론했던 폴더를 지워버렸다면 `git clone`부터 다시 하면 된다.

## 제거

```bash
rm -rf ~/.claude/skills/lawlint
```

Windows는 `Remove-Item -Recurse -Force "$env:USERPROFILE\.claude\skills\lawlint"`.
