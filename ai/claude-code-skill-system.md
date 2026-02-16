# Claude Code 스킬 시스템으로 포트폴리오 자동화 구축하기

> Claude Code의 글로벌 스킬(Slash Command)을 만들어 TIL 기록 → 블로그 확장 → 학습 리뷰 → README 개선까지 자동화하는 시스템을 구축했다.

**날짜**: 2026-02-16
**프로젝트**: 개인 학습 / 포트폴리오 자동화

## 핵심 내용

- **스킬(Skill)은 재사용 가능한 프롬프트 템플릿이다**: `~/.claude/skills/[스킬명]/SKILL.md`에 마크다운으로 작성하면 `/스킬명`으로 호출 가능
- **CLAUDE.md는 글로벌 지침 파일이다**: `~/.claude/CLAUDE.md`에 작성하면 모든 프로젝트에서 Claude Code가 참조하는 기본 규칙이 된다
- **에이전트 팀 병렬 실행이 핵심이다**: `Task` 도구로 여러 서브에이전트를 동시에 실행하여 리서치 시간을 절반으로 줄인다
- **프로젝트별 메모리가 분리된다**: `~/.claude/projects/[프로젝트경로]/memory/MEMORY.md`에 프로젝트별 컨텍스트가 유지된다
- **설정은 `settings.json`에서 관리한다**: 환경변수, 언어, 업데이트 채널 등을 JSON으로 설정

## 디렉토리 구조

```
~/.claude/
├── CLAUDE.md                    # 글로벌 지침 (모든 세션에 적용)
├── settings.json               # 환경 설정
├── skills/                     # 글로벌 스킬
│   ├── til/SKILL.md            # /til 명령
│   ├── blog/SKILL.md           # /blog 명령
│   ├── review/SKILL.md         # /review 명령
│   └── update-readme/SKILL.md  # /update-readme 명령
├── projects/                   # 프로젝트별 메모리
│   └── [프로젝트경로]/
│       └── memory/MEMORY.md
├── teams/                      # 에이전트 팀 (런타임)
└── tasks/                      # 태스크 리스트 (런타임)
```

## 스킬 작성법

### SKILL.md 기본 구조

```markdown
# 스킬 제목

스킬 설명과 사용법

## 사용법
/스킬명 [인자]

## Phase 1: 입력 처리
[인자 파싱, 주제 파악 로직]

## Phase 2: 리서치
[에이전트를 활용한 정보 수집]

## Phase 3: 실행
[실제 작업 수행]

## Phase 4: 출력
[결과 저장, Git push 등]
```

### 핵심 설계 패턴

| 패턴 | 설명 | 예시 |
|------|------|------|
| 병렬 에이전트 | Task 도구로 2개 이상 에이전트 동시 실행 | Agent A: 코드 탐색, Agent B: 중복 체크 |
| Phase 분리 | 입력 → 리서치 → 실행 → 출력으로 단계 분리 | /til의 5단계 워크플로우 |
| 템플릿 분기 | 입력 조건에 따라 다른 템플릿 적용 | 일반 TIL vs 트러블슈팅 TIL |
| 품질 게이트 | 저장 전 체크리스트 검증 | 한줄 요약, 코드 예시, 태그 검증 |
| Git 자동화 | 결과물을 자동으로 commit & push | `git add -A && git commit && git push` |

## 구축한 4개 스킬 요약

```
/til [주제]        → TIL 기록 + GitHub push (매일)
                     5단계: 주제 파악 → 병렬 리서치 → 작성 → 품질 체크 → Push

/blog [TIL명]      → TIL을 블로그 글로 확장 (매주)
                     TIL의 3~5배 분량, Mermaid 다이어그램, SEO 최적화

/review [week|month] → 학습 리뷰 생성 (매주)
                     카테고리 분포, TOP 3, 성장/부족 분석, 추천사항

/update-readme      → README를 포트폴리오 수준으로 개선
                     30초 룰, 기술적 챌린지 강조, 아키텍처 다이어그램
```

## 왜 / 언제 쓰는가

- **반복 작업을 자동화하고 싶을 때**: 매일 TIL 쓰기, 매주 블로그/리뷰 작성 같은 루틴을 한 줄 명령으로 줄일 수 있다
- **일관된 품질을 유지하고 싶을 때**: 템플릿과 품질 게이트가 있어 매번 같은 수준의 결과물이 나온다
- **포트폴리오를 체계적으로 쌓고 싶을 때**: 학습 → 기록 → 정리 → 공유까지 파이프라인으로 연결된다

## 주의할 점

- **SKILL.md에 allowed-tools를 명시해야 한다**: 스킬이 사용할 수 있는 도구를 제한하지 않으면 의도치 않은 동작이 발생할 수 있다
- **에이전트 팀 기능은 실험적이다**: `settings.json`에 `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS: "1"` 설정이 필요하다
- **스킬 파일명은 반드시 SKILL.md여야 한다**: 다른 이름은 인식되지 않는다
- **프로젝트별 CLAUDE.md도 있다**: 프로젝트 루트의 `CLAUDE.md`는 해당 프로젝트에서만 적용된다 (글로벌과 별도)

## 참고

- [Claude Code 공식 문서](https://docs.anthropic.com/en/docs/claude-code)
- [Claude Code GitHub](https://github.com/anthropics/claude-code)

---
`#claude-code` `#skill-system` `#automation` `#portfolio` `#productivity`
