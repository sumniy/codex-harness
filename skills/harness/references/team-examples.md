# Agent Team Examples

---

## 예시 1: 리서치 팀 (서브 에이전트 모드)

### 팀 아키텍처: 팬아웃/팬인
### 실행 모드: 서브 에이전트 (병렬 실행 + 파일 기반 데이터 전달)

```
[오케스트레이터]
    ├── Agent(official-researcher) → _workspace/research_official.md
    ├── Agent(media-researcher) → _workspace/research_media.md
    ├── Agent(community-researcher) → _workspace/research_community.md
    └── Agent(background-researcher) → _workspace/research_background.md
    (4개 에이전트 병렬 실행)
    → 오케스트레이터가 4개 산출물 Read → 종합 보고서 생성
```

### 에이전트 구성

| 에이전트 | 에이전트 타입 | 역할 | 출력 |
|---------|-------------|------|------|
| official-researcher | default | 공식 문서/블로그 | research_official.md |
| media-researcher | default | 미디어/투자 | research_media.md |
| community-researcher | default | 커뮤니티/SNS | research_community.md |
| background-researcher | default | 배경/경쟁/학술 | research_background.md |
| (오케스트레이터) | — | 통합 보고서 | 종합보고서.md |

> 리서치 에이전트는 `default` 빌트인 타입을 사용하되, 반드시 `.codex/agents/{name}.toml` 파일로 정의한다. 파일에는 역할·조사 범위·출력 파일 규격을 명시하여 재사용성과 결과물 품질을 보장한다.

### 오케스트레이터 워크플로우 (서브 에이전트)

```
Phase 1: 준비
  - 사용자 입력 분석 (주제, 조사 모드 파악)
  - _workspace/ 생성

Phase 2: 병렬 에이전트 실행 (팬아웃)
  - Agent(official-researcher, prompt: "공식 채널 조사. 결과를 _workspace/research_official.md에 저장")
  - Agent(media-researcher, prompt: "미디어/투자 동향 조사. 결과를 _workspace/research_media.md에 저장")
  - Agent(community-researcher, prompt: "커뮤니티 반응 조사. 결과를 _workspace/research_community.md에 저장")
  - Agent(background-researcher, prompt: "배경 환경 조사. 결과를 _workspace/research_background.md에 저장")
  → 4개 에이전트가 독립적으로 병렬 실행
  → 각 에이전트는 완료 시 지정된 파일에 결과 저장

Phase 3: 통합 (팬인)
  - 오케스트레이터가 4개 산출물 Read
  - 종합 보고서 생성
  - 상충 정보는 출처 병기

Phase 4: 정리
  - _workspace/ 보존 (사후 검증·감사 추적용)
```

### 데이터 전달 패턴

```
official-researcher  → _workspace/research_official.md    → 오케스트레이터 Read
media-researcher     → _workspace/research_media.md       → 오케스트레이터 Read
community-researcher → _workspace/research_community.md   → 오케스트레이터 Read
background-researcher→ _workspace/research_background.md  → 오케스트레이터 Read

교차 참조가 필요한 경우:
  오케스트레이터가 통합 단계에서 상충/관련 정보를 직접 교차 분석
```

---

## 예시 2: SF 소설 집필 팀 (서브 에이전트 모드)

### 팀 아키텍처: 파이프라인 + 팬아웃
### 실행 모드: 서브 에이전트 (병렬 + 순차 결합)

```
Phase 1 (병렬 — 팬아웃): worldbuilder + character-designer + plot-architect
  → 각자 _workspace/에 결과 저장, 오케스트레이터가 일관성 검증
Phase 2 (순차 — 파이프라인): prose-stylist가 Phase 1 산출물을 Read하여 집필
Phase 3 (병렬 — 팬아웃): science-consultant + continuity-manager가 draft를 Read하여 리뷰
Phase 4 (순차 — 파이프라인): prose-stylist가 리뷰 결과를 Read하여 최종 수정
```

### 에이전트 구성

| 에이전트 | 에이전트 타입 | 역할 | 스킬 |
|---------|-------------|------|------|
| worldbuilder | 커스텀 | 세계관 구축 | world-setting |
| character-designer | 커스텀 | 캐릭터 설계 | character-profile |
| plot-architect | 커스텀 | 플롯 구조 | outline |
| prose-stylist | 커스텀 | 문체 편집 + 집필 | write-scene, review-chapter |
| science-consultant | 커스텀 | 과학 검증 | science-check |
| continuity-manager | 커스텀 | 일관성 검증 | consistency-check |

### 에이전트 파일 전문 예시: `worldbuilder.toml`

```toml
name = "worldbuilder"
description = "SF 소설의 세계관을 구축하는 전문가. 물리 법칙, 사회 구조, 기술 수준, 역사를 설계한다."
developer_instructions = """
# Worldbuilder — SF 세계관 설계 전문가

당신은 SF 소설의 세계관 설계 전문가입니다. 과학적 사실에 기반하되 상상력을 확장하여, 이야기가 펼쳐질 세계의 물리적·사회적·기술적 토대를 구축합니다.

## 핵심 역할
1. 세계의 물리 법칙과 기술 수준 정의
2. 사회 구조, 정치 체계, 경제 시스템 설계
3. 역사적 맥락과 현재 갈등 구조 수립
4. 장소별 환경과 분위기 묘사

## 작업 원칙
- 내적 일관성 최우선 — 설정 간 모순이 없어야 한다
- "만약 이 기술이 있다면?" 연쇄 질문으로 세계의 파급 효과를 추론
- 이야기에 봉사하는 세계관 — 플롯을 방해하는 과도한 설정은 지양

## 입력/출력 프로토콜
- 입력: 사용자의 세계관 컨셉, 장르 요구사항
- 출력: `_workspace/01_worldbuilder_setting.md`
- 형식: 마크다운. 섹션별 (물리/사회/기술/역사/장소)

## 파일 기반 협업 프로토콜
- character-designer를 위해: 사회 구조, 계급 시스템, 직업군 정보를 출력 파일에 포함
- plot-architect를 위해: 세계의 주요 갈등 구조, 위기 요소를 출력 파일에 포함
- science-consultant의 리뷰 결과: `_workspace/03_science_review.md`를 Read하여 설정 수정
- 모든 관련 정보는 출력 파일에 명시적으로 기록하여 다른 에이전트가 참조 가능하게 함

## 에러 핸들링
- 컨셉이 모호하면 3가지 방향을 제안하고 선택 요청
- 과학적 오류 발견 시 대안을 함께 제시

## 협업
- character-designer에게 사회 구조 정보 제공 (출력 파일을 통해)
- plot-architect에게 갈등 구조 정보 제공 (출력 파일을 통해)
- science-consultant의 피드백을 반영하여 설정 수정 (리뷰 파일을 Read)
"""
model = "o3"
sandbox_mode = "workspace-write"
```

### 팀 워크플로우 상세

```
Phase 1: 병렬 팬아웃
  - Agent(worldbuilder, prompt: "세계관 구축. 결과를 _workspace/01_worldbuilder_setting.md에 저장")
  - Agent(character-designer, prompt: "캐릭터 설계. 결과를 _workspace/01_character_profiles.md에 저장")
  - Agent(plot-architect, prompt: "플롯 구조. 결과를 _workspace/01_plot_outline.md에 저장")
  → 3개 에이전트 병렬 실행, 각자 _workspace/에 결과 저장
  → 오케스트레이터가 3개 산출물을 Read하여 일관성 교차 검증
  → 불일치 발견 시 관련 에이전트를 재호출하여 수정

Phase 2: 순차 파이프라인 (단독 집필)
  - Agent(prose-stylist, prompt: "_workspace/의 3개 산출물을 Read하여 집필.
    결과를 _workspace/02_prose_draft.md에 저장")

Phase 3: 병렬 팬아웃 (리뷰)
  - Agent(science-consultant, prompt: "_workspace/02_prose_draft.md를 Read하여 과학 검증.
    결과를 _workspace/03_science_review.md에 저장")
  - Agent(continuity-manager, prompt: "_workspace/02_prose_draft.md와 Phase 1 산출물을 Read하여 일관성 검증.
    결과를 _workspace/03_continuity_review.md에 저장")
  → 2개 에이전트 병렬 실행
  → 오케스트레이터가 리뷰 결과를 Read하여 종합

Phase 4: 순차 파이프라인 (최종 수정)
  - Agent(prose-stylist, prompt: "_workspace/03_science_review.md와
    _workspace/03_continuity_review.md를 Read하여 리뷰 결과 반영 수정.
    최종 결과를 _workspace/04_final_draft.md에 저장")
```

---

## 예시 3: 웹툰 제작 팀 (서브 에이전트 모드)

### 팀 아키텍처: 생성-검증
### 실행 모드: 서브 에이전트 (순차 파이프라인 + 루프)

> 생성-검증 패턴에서 에이전트가 2개뿐이고, 결과 파일 전달이 핵심이므로 순차 서브 에이전트가 적합.

```
Phase 1: Agent(webtoon-artist) → _workspace/panels/ 에 패널 생성
Phase 2: Agent(webtoon-reviewer) → _workspace/review_report.md 에 검수 결과 저장
Phase 3: 오케스트레이터가 review_report.md를 Read → REDO 패널이 있으면
         Agent(webtoon-artist)를 재호출 (최대 2회)
```

### 에이전트 구성

| 에이전트 | 에이전트 타입 | 역할 | 스킬 |
|---------|-------------|------|------|
| webtoon-artist | 커스텀 | 패널 이미지 생성 | generate-webtoon |
| webtoon-reviewer | 커스텀 | 품질 검수 | review-webtoon, fix-webtoon-panel |

### 에이전트 파일 전문 예시: `webtoon-reviewer.toml`

```toml
name = "webtoon-reviewer"
description = "웹툰 패널의 품질을 검수하는 전문가. 구도, 캐릭터 일관성, 텍스트 가독성, 연출을 평가한다."
developer_instructions = """
# Webtoon Reviewer — 웹툰 품질 검수 전문가

당신은 웹툰 패널의 품질을 검수하는 전문가입니다. 시각적 완성도, 스토리 전달력, 캐릭터 일관성을 기준으로 패널을 평가합니다.

## 핵심 역할
1. 각 패널의 구도와 시각적 완성도 평가
2. 캐릭터 외형의 패널 간 일관성 검증
3. 말풍선 텍스트의 가독성과 배치 평가
4. 전체 에피소드의 연출 흐름과 페이싱 검토

## 작업 원칙
- PASS/FIX/REDO 3단계로 명확히 판정
- FIX는 부분 수정으로 해결 가능한 경우, REDO는 전면 재생성 필요
- 주관적 취향이 아닌 객관적 기준(일관성, 가독성, 구도)으로 판단

## 입력/출력 프로토콜
- 입력: `_workspace/panels/` 디렉토리의 패널 이미지들
- 출력: `_workspace/review_report.md`
- 형식:
  ```
  ## Panel {N}
  - 판정: PASS | FIX | REDO
  - 사유: [구체적 이유]
  - 수정 지시: [FIX/REDO인 경우 구체적 수정 방향]
  ```

## 에러 핸들링
- 이미지 로드 실패 시 해당 패널을 REDO로 판정
- 2회 재생성 후에도 REDO인 패널은 경고와 함께 PASS 처리

## 협업
- webtoon-artist에게 수정 지시서 전달 (review_report.md 파일을 통해)
- 오케스트레이터가 review_report.md를 Read하여 재생성 여부 결정
"""
model = "o3"
sandbox_mode = "workspace-write"
```

### 에러 핸들링

```
재시도 정책:
- 오케스트레이터가 review_report.md를 Read하여 REDO 판정 패널 식별
- REDO 패널 → artist에게 재생성 요청 (review_report.md의 수정 지시를 프롬프트에 포함)
- 최대 2회 루프 후 강제 PASS
- 전체 패널의 50% 이상이 REDO면 사용자에게 프롬프트 수정 제안
```

---

## 예시 4: 코드 리뷰 팀 (서브 에이전트 모드)

### 팀 아키텍처: 팬아웃/팬인 + 교차 분석
### 실행 모드: 서브 에이전트 (병렬 실행 + 오케스트레이터 교차 분석)

> 코드 리뷰는 서로 다른 관점의 리뷰어들이 병렬로 분석하고, 오케스트레이터가 교차 영역 이슈를 종합하여 더 깊은 리뷰가 가능.

```
[오케스트레이터] → 병렬 팬아웃
    ├── Agent(security-reviewer) → _workspace/review_security.md
    ├── Agent(performance-reviewer) → _workspace/review_performance.md
    └── Agent(test-reviewer) → _workspace/review_test.md
    → 오케스트레이터가 3개 리뷰 결과 Read
    → 교차 영역 이슈 분석 (예: 보안+성능, 성능+테스트 교차점)
    → 종합 리뷰 보고서 생성
```

### 교차 분석 패턴

```
오케스트레이터가 3개 리뷰 결과를 Read한 후 교차 분석:
  - review_security.md의 "SQL 쿼리 주입 가능" + review_performance.md의 쿼리 분석 → 교차 이슈 도출
  - review_performance.md의 "N+1 쿼리 발견" + review_test.md의 커버리지 → 테스트 누락 확인
  - review_test.md의 "인증 모듈 테스트 없음" + review_security.md의 보안 분석 → 우선순위 판단

교차 영역 이슈가 발견되면:
  오케스트레이터가 관련 리뷰어를 추가 호출하여 심층 분석 요청
  (예: Agent(security-reviewer, prompt: "performance-reviewer가 발견한 N+1 쿼리에 대해
       보안 관점에서 추가 분석. _workspace/review_performance.md 참조"))
```

핵심: 오케스트레이터가 **팬인 단계에서 교차 영역 이슈를 직접 분석**하고, 필요시 관련 에이전트를 추가 호출하여 심층 리뷰 수행.

---

## 예시 5: 감독자 패턴 — 코드 마이그레이션 팀 (서브 에이전트 모드)

### 팀 아키텍처: 감독자
### 실행 모드: 서브 에이전트 (동적 배치 할당 + 파일 기반 진행 관리)

```
[오케스트레이터/감독자] → 파일 목록 분석 → 배치 할당
    ├→ Agent(migrator-1, batch A) → _workspace/batch_a_result.md
    ├→ Agent(migrator-2, batch B) → _workspace/batch_b_result.md
    └→ Agent(migrator-3, batch C) → _workspace/batch_c_result.md
    ← 오케스트레이터가 결과 Read → 성공/실패 판단 → 추가 배치 할당 또는 재시도
```

### 에이전트 구성

| 에이전트 | 역할 |
|---------|------|
| (오케스트레이터 = migration-supervisor) | 파일 분석, 배치 분배, 진행 관리 |
| migrator-1~3 | 할당된 파일 배치를 마이그레이션 |

### 감독자의 동적 분배 로직 (서브 에이전트 활용)

```
1. 전체 대상 파일 목록 수집
2. 복잡도 추정 (파일 크기, import 수, 의존성)
3. 파일을 배치로 분할하여 _workspace/batch_assignments.md에 기록
4. 병렬 팬아웃: 각 migrator에게 배치 할당
   - Agent(migrator-1, prompt: "Batch A 파일들을 마이그레이션.
     대상: [파일 목록]. 결과를 _workspace/batch_a_result.md에 저장.
     성공/실패를 파일별로 명시")
   - Agent(migrator-2, prompt: "Batch B ...")
   - Agent(migrator-3, prompt: "Batch C ...")
5. 오케스트레이터가 결과 파일들을 Read:
   - 성공 → 다음 배치 할당 (새 에이전트 호출)
   - 실패 → 실패 원인 분석 후 재시도 또는 다른 migrator에게 재할당
6. 모든 배치 완료 → 오케스트레이터가 통합 테스트 실행
```

팬아웃과의 차이: 작업이 사전 고정이 아니라 **오케스트레이터가 결과를 보고 동적으로 다음 배치를 할당**한다. 결과 파일의 성공/실패 정보를 기반으로 감독자가 런타임에 판단.

---

## 산출물 패턴 요약

### 에이전트 정의 파일
위치: `프로젝트/.codex/agents/{agent-name}.toml`
필수 섹션 (developer_instructions 내): 핵심 역할, 작업 원칙, 입력/출력 프로토콜, 에러 핸들링, 협업
파일 기반 협업 추가 섹션: **파일 기반 협업 프로토콜** (입력 파일 경로, 출력 파일 경로, 다른 에이전트 산출물 참조 방법)

### 스킬 파일 구조
위치: `프로젝트/.agents/skills/{skill-name}/SKILL.md` (프로젝트 레벨)

### 통합 스킬 (오케스트레이터)
팀 전체를 조율하는 상위 스킬. 시나리오별 에이전트 구성과 워크플로우를 정의.
템플릿: `references/orchestrator-template.md` 참조.
**실행 모드:** 서브 에이전트 (Codex CLI는 서브 에이전트 모드만 지원).
**데이터 전달:** 파일 기반 (`_workspace/` 디렉토리를 통한 산출물 공유).
