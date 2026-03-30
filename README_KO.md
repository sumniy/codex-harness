<p align="center">
  <img src="harness_banner.png" alt="Harness Banner" width="600">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Version-1.0.1-brightgreen.svg" alt="Version">
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-Apache_2.0-blue.svg" alt="License"></a>
  <img src="https://img.shields.io/badge/Claude_Code-Plugin-purple.svg" alt="Claude Code Plugin">
  <img src="https://img.shields.io/badge/Codex_CLI-Skill-blue.svg" alt="Codex CLI Skill">
  <img src="https://img.shields.io/badge/Patterns-6_Architectures-orange.svg" alt="6 Architecture Patterns">
</p>

# Harness

**Agent Team & Skill Architect** — **Claude Code**와 **OpenAI Codex CLI** 모두 지원

[English](README.md) | **한국어** | [日本語](README_JA.md)

> [revfactory/harness](https://github.com/revfactory/harness)에서 포크. 하나의 저장소에서 Claude Code와 Codex CLI 모두 설치 가능.

도메인/프로젝트에 맞는 하네스를 구성하고, 전문 에이전트를 정의하며, 에이전트가 사용할 스킬을 생성하는 메타 스킬.

## 설치

### Claude Code

```shell
# 마켓플레이스 등록 후 설치
/plugin marketplace add sumniy/codex-harness
/plugin install harness@harness

# 또는 직접 복사
cp -r skills/claude-code/harness ~/.claude/skills/harness
```

### OpenAI Codex CLI

```
# $skill-installer로 설치
$skill-installer install https://github.com/sumniy/codex-harness/tree/main/skills/codex/harness

# 또는 직접 복사
cp -r skills/codex/harness ~/.agents/skills/harness
```

## 저장소 구조

```
harness/
├── .claude-plugin/                    # Claude Code 플러그인 매니페스트
│   ├── plugin.json
│   └── marketplace.json
├── .codex-plugin/                     # Codex CLI 플러그인 매니페스트
│   ├── plugin.json
│   └── marketplace.json
│
├── skills/
│   ├── claude-code/                   # ← Claude Code 버전 (원본)
│   │   └── harness/
│   │       ├── SKILL.md
│   │       └── references/ (6개 파일)
│   │
│   └── codex/                         # ← Codex CLI 버전 (적응)
│       └── harness/
│           ├── SKILL.md
│           ├── agents/openai.yaml
│           └── references/ (6개 파일)
│
├── README.md / README_KO.md / README_JA.md
```

## 플랫폼별 비교

| | Claude Code | Codex CLI |
|---|---|---|
| **스킬 경로** | `skills/claude-code/harness/` | `skills/codex/harness/` |
| **에이전트 정의** | `.claude/agents/{name}.md` | `.codex/agents/{name}.toml` |
| **스킬 정의** | `.claude/skills/{name}/skill.md` | `.agents/skills/{name}/SKILL.md` |
| **실행 모드** | Agent Teams (기본) + 서브에이전트 | 서브에이전트 (`max_threads`로 병렬) |
| **최상위 모델** | `model: "opus"` | `model = "o3"` / `gpt-5.4` |
| **설치 명령** | `/plugin install` | `$skill-installer install <url>` |
| **호출** | `/harness` 또는 자동 트리거 | `$harness` 또는 자동 트리거 |
| **프로젝트 지침** | `CLAUDE.md` | `AGENTS.md` |

## 핵심 기능

- **6가지 아키텍처 패턴** — 파이프라인, 팬아웃/팬인, 전문가 풀, 생성-검증, 감독자, 계층적 위임
- **스킬 생성** — Progressive Disclosure로 컨텍스트 효율 관리
- **오케스트레이션** — 에이전트 간 데이터 전달, 에러 핸들링, 조율 프로토콜
- **검증 체계** — 트리거 검증, 드라이런 테스트, With-skill vs Without-skill 비교

## 사용법

**Claude Code:**
```
하네스 구성해줘
Build a harness for this project
```

**Codex CLI:**
```
$harness
하네스 구성해줘
Build a harness for this project
```

## 크레딧

- 원본: [revfactory/harness](https://github.com/revfactory/harness) by [@revfactory](https://github.com/revfactory)
- 연구: [revfactory/claude-code-harness](https://github.com/revfactory/claude-code-harness) — +60% 품질 향상
- Harness 100: [revfactory/harness-100](https://github.com/revfactory/harness-100) — 10개 도메인 100개 프리빌트 하네스

## 라이선스

Apache 2.0
