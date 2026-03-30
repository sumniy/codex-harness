<p align="center">
  <img src="harness_banner.png" alt="Harness Banner" width="600">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Version-1.0.0-brightgreen.svg" alt="Version">
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-Apache_2.0-blue.svg" alt="License"></a>
  <img src="https://img.shields.io/badge/Codex_CLI-Skill-blue.svg" alt="Codex CLI Skill">
  <img src="https://img.shields.io/badge/Patterns-6_Architectures-orange.svg" alt="6 Architecture Patterns">
  <img src="https://img.shields.io/badge/Mode-Subagents-green.svg" alt="Subagents">
</p>

# Harness for Codex

**Agent Team & Skill Architect** — OpenAI Codex CLI 스킬

[English](README.md) | **한국어**

> [revfactory/harness](https://github.com/revfactory/harness) (Claude Code 플러그인)를 OpenAI Codex CLI용으로 적응한 버전.

도메인/프로젝트에 맞는 하네스를 구성하고, 전문 에이전트를 정의하며, 에이전트가 사용할 스킬을 생성하는 메타 스킬.

## 개요

Harness는 Codex CLI의 서브에이전트 시스템을 활용하여 복잡한 작업을 전문 에이전트 팀으로 분해·조율하는 아키텍처 도구다. "하네스 구성해줘"라고 말하면, 사용자의 도메인에 맞는 에이전트 정의(`.codex/agents/`)와 스킬(`.agents/skills/`)을 자동 생성한다.

## 핵심 기능

- **에이전트 팀 설계** — 파이프라인, 팬아웃/팬인, 전문가 풀, 생성-검증, 감독자, 계층적 위임 등 6가지 아키텍처 패턴 지원
- **스킬 생성** — Progressive Disclosure 패턴으로 컨텍스트를 효율 관리하는 스킬 자동 생성
- **오케스트레이션** — 파일 기반 에이전트 간 데이터 전달, 에러 핸들링, 조율 프로토콜 포함
- **검증 체계** — 트리거 검증, 드라이런 테스트, With-skill vs Without-skill 비교 테스트

## 워크플로우

```
Phase 1: 도메인 분석
    ↓
Phase 2: 팀 아키텍처 설계 (서브에이전트 병렬 구성)
    ↓
Phase 3: 에이전트 정의 생성 (.codex/agents/)
    ↓
Phase 4: 스킬 생성 (.agents/skills/)
    ↓
Phase 5: 통합 및 오케스트레이션
    ↓
Phase 6: 검증 및 테스트
```

## 설치

### $skill-installer로 설치 (권장)

```
$skill-installer install https://github.com/sumniy/codex-harness/tree/main/skills/harness
```

### 글로벌 스킬로 직접 설치

```shell
# skills 디렉토리를 ~/.agents/skills/harness/에 복사
cp -r skills/harness ~/.agents/skills/harness
```

### 플러그인으로 설치 (marketplace.json 등록)

`~/.agents/plugins/marketplace.json`에 추가:

```json
{
  "name": "codex-harness-marketplace",
  "interface": { "displayName": "Codex Harness" },
  "plugins": [
    {
      "name": "harness",
      "source": { "source": "local", "path": "/path/to/codex-harness" },
      "policy": { "installation": "AVAILABLE" },
      "category": "Productivity"
    }
  ]
}
```

Codex CLI에서 `/plugins`로 브라우징.

## 플러그인 구조

```
codex-harness/
├── .codex-plugin/
│   ├── plugin.json                    # 플러그인 매니페스트
│   └── marketplace.json               # 마켓플레이스 레지스트리
├── skills/
│   └── harness/
│       ├── SKILL.md                   # 메인 스킬 정의 (6 Phase 워크플로우)
│       ├── agents/
│       │   └── openai.yaml            # 스킬 메타데이터 & 정책
│       └── references/
│           ├── agent-design-patterns.md   # 6가지 아키텍처 패턴
│           ├── orchestrator-template.md   # 서브에이전트 오케스트레이터 템플릿
│           ├── team-examples.md           # 실전 팀 구성 예시 5종
│           ├── skill-writing-guide.md     # 스킬 작성 가이드
│           ├── skill-testing-guide.md     # 테스트/평가 방법론
│           └── qa-agent-guide.md          # QA 에이전트 통합 가이드
└── README.md
```

## 사용법

Codex CLI에서 다음과 같이 트리거한다:

```
하네스 구성해줘
$harness
Build a harness for this project
```

### 실행 모드

Codex는 **서브에이전트 병렬 실행**을 기본 실행 모드로 사용한다. `.codex/config.toml`의 `max_threads`로 동시 실행 수를 제어하며, 에이전트 간 데이터 교환은 `_workspace/` 디렉토리의 파일을 통해 수행한다.

### 아키텍처 패턴

| 패턴 | 설명 |
|------|------|
| 파이프라인 | 순차 의존 작업 |
| 팬아웃/팬인 | 병렬 독립 작업 |
| 전문가 풀 | 상황별 선택 호출 |
| 생성-검증 | 생성 후 품질 검수 |
| 감독자 | 중앙 에이전트가 동적 분배 |
| 계층적 위임 | 상위→하위 재귀적 위임 |

## 산출물

하네스가 생성하는 파일:

```
프로젝트/
├── .codex/
│   ├── config.toml         # 에이전트 설정
│   └── agents/             # 에이전트 정의 파일 (TOML)
│       ├── analyst.toml
│       ├── builder.toml
│       └── qa.toml
├── .agents/
│   └── skills/             # 스킬 파일
│       ├── analyze/
│       │   └── SKILL.md
│       └── build/
│           ├── SKILL.md
│           └── references/
└── AGENTS.md               # 프로젝트 컨텍스트 & 에이전트 라우팅
```

## 사용 사례 — 이 프롬프트를 그대로 사용하세요

Harness 설치 후 아래 프롬프트를 Codex CLI에 복사해서 사용하세요:

**딥 리서치**
```
리서치 하네스를 구성해줘. 어떤 주제든 여러 각도에서 조사할 수 있는 에이전트가
필요해 — 웹 검색, 학술 자료, 커뮤니티 반응 — 교차 검증 후 종합 보고서를 작성.
```

**웹사이트 제작**
```
풀스택 웹사이트 개발 하네스를 구성해줘. 디자인, 프론트엔드(React/Next.js),
백엔드(API), QA 테스트를 와이어프레임부터 배포까지 파이프라인으로 조율.
```

**코드 리뷰**
```
종합 코드 리뷰 하네스를 구성해줘. 아키텍처, 보안 취약점, 성능 병목, 코드 스타일을
병렬로 감사하는 에이전트들이 결과를 하나의 리포트로 통합.
```

## Claude Code ↔ Codex 매핑

| Claude Code | Codex CLI |
|-------------|-----------|
| `CLAUDE.md` | `AGENTS.md` |
| `.claude/agents/{name}.md` | `.codex/agents/{name}.toml` |
| `.claude/skills/{name}/skill.md` | `.agents/skills/{name}/SKILL.md` |
| `/plugin marketplace add` | `$skill-installer install <url>` |
| `/plugin install` | `/plugins` 또는 직접 복사 |
| Agent Teams (TeamCreate) | 서브에이전트 병렬 실행 |
| `model: "opus"` | 최상위 모델 (o3, gpt-5.4 등) |

## 크레딧

- 원본: [revfactory/harness](https://github.com/revfactory/harness) by [@revfactory](https://github.com/revfactory)
- 연구: [revfactory/claude-code-harness](https://github.com/revfactory/claude-code-harness) — 하네스 적용 시 +60% 품질 향상

## 요구사항

- [Codex CLI](https://github.com/openai/codex) 설치 (`npm i -g @openai/codex`)

## 라이선스

Apache 2.0
