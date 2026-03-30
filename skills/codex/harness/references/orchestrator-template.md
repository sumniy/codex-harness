# 오케스트레이터 스킬 템플릿

오케스트레이터는 서브에이전트를 조율하는 상위 스킬이다. Codex CLI는 파일 기반 데이터 전달을 통한 서브에이전트 모드를 사용한다.

---

## 서브에이전트 모드

서브에이전트를 직접 호출하고, 결과를 파일로 전달받는다. 모든 데이터 교환은 파일 시스템을 통해 이루어진다.

```markdown
---
name: {domain}-orchestrator
description: "{도메인} 에이전트를 조율하는 오케스트레이터. {트리거 키워드}."
---

# {Domain} Orchestrator

{도메인}의 에이전트를 조율하여 {최종 산출물}을 생성하는 통합 스킬.

## 실행 모드: 서브에이전트

## 에이전트 구성

에이전트는 `.codex/agents/{name}.toml`에 정의하거나, `.codex/config.toml`의 `[agents]` 섹션에서 구성한다.

| 에이전트 | 에이전트 정의 | 역할 | 스킬 | 출력 |
|---------|-------------|------|------|------|
| {agent-1} | `.codex/agents/{agent-1}.toml` | {역할} | {skill} | {output-file} |
| {agent-2} | `.codex/agents/{agent-2}.toml` | {역할} | {skill} | {output-file} |
| ... | | | | |

### 에이전트 정의 파일 예시

`.codex/agents/{agent-name}.toml`:
```toml
[agent]
name = "{agent-name}"
role = "{역할 설명}"
model = "최상위 모델"

[agent.skills]
paths = [".agents/skills/{skill-name}/SKILL.md"]
```

### Codex 설정 참조

`.codex/config.toml`의 `[agents]` 섹션에서 전역 에이전트 설정을 관리:
```toml
[agents]
default_model = "최상위 모델"
workspace_dir = "_workspace"

[agents.{agent-1}]
definition = ".codex/agents/{agent-1}.toml"

[agents.{agent-2}]
definition = ".codex/agents/{agent-2}.toml"
```

## 스킬 정의

스킬은 `.agents/skills/{skill-name}/SKILL.md`에 정의한다.

## 워크플로우

### Phase 1: 준비
1. 사용자 입력 분석 — {무엇을 파악하는지}
2. 작업 디렉토리에 `_workspace/` 생성
3. 입력 데이터를 `_workspace/00_input/`에 저장

### Phase 2: {주요 작업 — 예: 조사/생성/분석}

**실행 방식:** {병렬 | 순차 | 조건부}

{병렬인 경우}
N개 서브에이전트를 동시 호출. 각 에이전트는 입력 파일을 읽고 출력 파일을 작성한다:

| 에이전트 | 입력 파일 | 출력 파일 | model |
|---------|----------|----------|-------|
| {agent-1} | `_workspace/00_input/{input}.md` | `_workspace/{phase}_{agent}_{artifact}.md` | 최상위 모델 |
| {agent-2} | `_workspace/00_input/{input}.md` | `_workspace/{phase}_{agent}_{artifact}.md` | 최상위 모델 |

> **데이터 전달 방식:** 서브에이전트 간 모든 데이터 교환은 `_workspace/` 디렉토리 내 파일을 통해 이루어진다. 에이전트는 지정된 입력 파일을 Read로 읽고, 결과를 지정된 출력 파일에 Write로 저장한다.

{순차인 경우}
이전 에이전트의 출력 파일을 다음 에이전트의 입력 파일로 전달:

1. {agent-1} 실행 → `_workspace/01_{artifact}.md` 생성
2. {agent-2} 실행 (입력: `_workspace/01_{artifact}.md`) → `_workspace/02_{artifact}.md` 생성

### Phase 3: {후속 작업 — 예: 검증/통합}
1. Phase 2의 산출물 파일을 Read로 수집
2. {통합/검증 로직}
3. 최종 산출물 생성: `{output-path}/{filename}`

### Phase 4: 정리
1. `_workspace/` 디렉토리 보존 (중간 산출물은 삭제하지 않음 — 사후 검증·감사 추적용)
2. 사용자에게 결과 요약 보고

## 데이터 흐름

```
입력 파일 → [agent-1] → artifact-1.md ─┐
                                        ├→ [오케스트레이터: Read + 통합] → 최종 산출물
입력 파일 → [agent-2] → artifact-2.md ─┘
```

> **핵심 원칙:** 모든 에이전트 간 통신은 파일 기반이다. 에이전트 A의 결과가 에이전트 B에 필요하면, A가 파일에 쓰고 B가 해당 파일을 읽는다. 직접적인 메시지 전달 메커니즘은 없다.

## 에러 핸들링

| 상황 | 전략 |
|------|------|
| 에이전트 1개 실패 | 1회 재시도. 재실패 시 해당 결과 없이 진행, 보고서에 누락 명시 |
| 에이전트 과반 실패 | 사용자에게 알리고 진행 여부 확인 |
| 타임아웃 | 현재까지 수집된 부분 결과 사용 |
| 에이전트 간 데이터 충돌 | 출처 명시 후 병기, 삭제하지 않음 |
| 출력 파일 미생성 | 에이전트 재호출. 재실패 시 빈 결과로 처리하고 보고서에 명시 |
| 입력 파일 읽기 실패 | 파일 존재 여부 확인 후 재시도. 파일 부재 시 이전 Phase 재실행 검토 |

## 테스트 시나리오

### 정상 흐름
1. 사용자가 {입력}을 제공
2. Phase 1에서 {분석 결과} 도출
3. Phase 2에서 {N}개 에이전트가 병렬 실행, 각각 `_workspace/`에 산출물 파일 생성
4. Phase 3에서 산출물 파일을 Read로 수집하여 최종 보고서 생성
5. 예상 결과: `{output-path}/{filename}` 생성

### 에러 흐름
1. Phase 2에서 {agent-2}가 실패 (출력 파일 미생성)
2. 1회 재시도 후에도 실패
3. {agent-2} 결과 없이 나머지 결과로 Phase 3 진행
4. 최종 보고서에 "{agent-2} 영역 데이터 미수집" 명시
5. 사용자에게 부분 완료 알림
```

---

## 작성 원칙

1. **파일 기반 데이터 전달** — 모든 에이전트 간 통신은 `_workspace/` 내 파일을 통해 이루어짐. 직접 메시지 전달 없음
2. **에이전트 정의는 `.codex/agents/{name}.toml`에** — 역할, 모델, 스킬 경로를 TOML로 명시
3. **스킬 정의는 `.agents/skills/{name}/SKILL.md`에** — 스킬별 지침을 마크다운으로 작성
4. **Codex 설정은 `.codex/config.toml`의 `[agents]` 섹션에** — 전역 에이전트 설정 관리
5. **파일 경로는 절대적으로** — 상대 경로 금지, `_workspace/` 기준 명확한 경로
6. **Phase 간 의존성 명시** — 어떤 Phase가 어떤 Phase의 결과 파일에 의존하는지
7. **에러 핸들링은 현실적으로** — "모든 것이 성공한다"고 가정하지 않음
8. **테스트 시나리오 필수** — 정상 1 + 에러 1 이상

## 실제 오케스트레이터 참고

팬아웃/팬인 패턴의 오케스트레이터 기본 구조:
준비 → N개 서브에이전트 병렬 실행 (파일 출력) → Read + 통합 → 정리.
