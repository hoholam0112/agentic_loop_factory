# Agentic Loop Factory

코딩 에이전트(Claude Code)로 반복 작업을 자동화하는 **에이전틱 루프**를 만들고 관리하는 저장소입니다.

각 루프는 다음 패턴으로 만들어집니다:

1. **프롬프트 작성** (`prompts/`) — 루프의 운영 원칙, 워크플로우, 완료조건을 정의한 요구사항 문서
2. **스킬 빌드** (`skills/`) — 프롬프트를 기반으로 구현한 Claude Code 스킬. 프롬프트의 모든 요구사항이 스킬에 반영되었는지 traceability를 검증하며 유지보수합니다.

## 저장소 구조

```
prompts/
  build-experiment-loop.md   # experiment-loop의 요구사항 정의 (source of truth)
skills/
  experiment-loop/           # ML 실험 자동화 루프 스킬
    SKILL.md                 # 진입점: 대시보드, 단계 정의, state.json 스키마
    references/              # 단계별 상세 절차 (진입 시 로드)
    templates/               # 문서 템플릿 6종 (PRD, 설계, 스펙, 계획, 보고서)
```

## experiment-loop

ML 실험을 설계 → 구현 → 실행 → 정리까지 반복 수행하는 루프입니다.

- **단계**: 0 bootstrap(프로젝트 온보딩, 최초 1회) → 0.5 long-term plan(필요 시) → 1 실험 설계 → 2 기술 설계 → 3 구현(TDD) → 4 실험 수행 및 보고 → 5 마무리(GC, wiki 갱신, tools/hooks 리뷰)
- **루프 제어**: 메인 세션이 진행을 담당하고, 검증은 subagent 게이트로 분리. 진행 상황은 `docs/loops/<loop-id>/state.json`에 기록되어 중단 후 재개와 다음 루프 인계를 지원
- **검증 게이트**: 이슈를 Critical/Major/Minor로 분류(실험 유효성·재현성 기준), 최대 3회 자동 수정 후 에스컬레이션
- **설계 원칙**: 에이전트에게 "무엇을, 왜"를 전달하고 "어떻게"는 위임 / 코드 그라운딩 필수 / claim 단위 출처 명시 / 점진적 컨텍스트 로드

## 사용 방법

대상 프로젝트에서 스킬을 사용하려면 `skills/experiment-loop/`를 프로젝트의 `.claude/skills/` (또는 개인용 `~/.claude/skills/`)에 복사하거나 심볼릭 링크한 뒤, Claude Code에서 `/experiment-loop`로 호출합니다. 스킬은 호출 시 대시보드로 동작해 현재 루프 상태를 보여주고 다음 작업을 안내합니다.

## 새 루프 추가하기

1. `prompts/build-<loop-name>.md`에 요구사항 프롬프트를 작성합니다 (목적·원칙 / 실행 메커니즘 / 워크플로우 / 주의사항).
2. 프롬프트를 기반으로 `skills/<loop-name>/`에 스킬을 빌드합니다. SKILL.md는 얇게 유지하고 상세 절차는 `references/`에 분리합니다.
3. 프롬프트의 모든 요구사항이 스킬에 반영되었는지 대조 검토하고, 루프 종료 조건(둠루프 방지)과 crash-resume 안전성을 점검합니다.

## 언어 규칙

- `prompts/`의 요구사항 문서: 한국어
- `skills/`의 모든 스킬 파일: 영어 (Claude Code 스킬 규약)
