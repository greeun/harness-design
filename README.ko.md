# Harness Design — 웹사이트 & 앱 빌더

> **v1.0.0**

짧은 아이디어를 Anthropic의 **Planner → Generator → Evaluator** 3-에이전트 하네스를 통해 완성된 웹사이트/앱으로 만드는 Claude Code 스킬.

[Harness Design for Long-Running Application Development](https://www.anthropic.com/engineering/harness-design-long-running-apps) (Anthropic Engineering, 2026) 기반.

## 작동 방식

주제나 요구사항을 입력하면 자율 빌드 루프가 실행됩니다:

```
사용자 브리프 → Planner (spec.md) → Generator (구현) → Evaluator (QA) → 완료까지 반복
```

각 에이전트는 **독립 서브에이전트**로 자체 컨텍스트 윈도우에서 실행됩니다. 파일로만 소통하며 대화를 공유하지 않고, 자기 평가도 하지 않습니다.

## 왜 하네스인가?

| 문제 | 하네스 없이 | 하네스 있을 때 |
|---|---|---|
| **자기평가 편향** | 에이전트가 자기 UI를 칭찬 | 별도 Evaluator가 적대적으로 채점 |
| **컨텍스트 불안** | 컨텍스트 차면 성급히 마무리 | 깨끗한 핸드오프로 컨텍스트 리셋 |
| **제네릭 "AI 슬롭" 디자인** | 히어로 + 3단 컬럼 + 그라디언트 템플릿 | Design Quality & Originality 2배 가중 루브릭 |
| **스텁 기능** | 버튼은 있는데 작동 안 함 | Evaluator가 end-to-end 인터랙션 검증 |

## 주요 기능

- **UI/UX 최적화** — Planner가 디자인 토큰, 정보 아키텍처, 반응형 전략, 접근성 요구사항 출력
- **디자인 시스템 우선** — Generator가 컴포넌트 구현 전 CSS 변수로 토큰 구현
- **9축 루브릭** — Design Quality (2배), Originality (2배), Craft, Functionality, Responsive, Accessibility, Interaction Design, Visual Hierarchy, UX Heuristics
- **Nielsen 10원칙** 휴리스틱 평가 내장
- **반응형 테스트** — Playwright로 375px / 768px / 1280px 뷰포트 스크린샷
- **접근성 검증** — WCAG AA 대비비, 키보드 네비, 포커스 표시, ARIA, axe-core
- **방향 전환 통제** — Evaluator의 명시적 `REDIRECT` 없이 Generator가 디자인 방향을 바꿀 수 없음
- **V1/V2 모드** — 전체 스프린트 루프 (Sonnet) 또는 간소화 단일 패스 (Opus)

## 파일 핸드오프

```
spec.md              Planner → Generator, Evaluator
sprint_contract.md   Generator ↔ Evaluator (협상)
generator_report.md  Generator → Evaluator
critique.md          Evaluator → Generator (REDIRECT 권한 포함)
design_memo.md       Generator → 다음 세션 (기억상실 피벗 방지)
handoff.md           Generator → 다음 세션 (남은 작업)
```

## Anthropic 실험에서 얻은 교훈

- **10회차 창의적 도약** — 네덜란드 미술관 사이트가 10회차에 다크 랜딩에서 CSS 퍼스펙티브 3D 공간으로 전환. 후기 피벗은 돌파구일 수 있지만 루브릭 근거가 필요.
- **중간 반복이 최고일 때도** — 최종본 ≠ 최고본. 매 작동 증분마다 git 커밋하여 롤백 가능.
- **프롬프팅이 캐릭터를 결정** — "museum quality"라 쓰면 전부 미술관 같아짐. 루브릭은 참조가 아닌 품질을 기술해야.
- **Evaluator 자기 설득** — 튜닝 안 된 Evaluator는 문제를 찾고도 "별거 아니다"며 승인. 여러 튜닝 사이클 필요.
- **핵심 인터랙션 스텁** — 버튼은 있는데 작동 안 함. "UI 존재" ≠ "인터랙션 end-to-end 작동."

## 비용/시간 벤치마크 (논문 기준)

| 구성 | 시간 | 비용 | 결과 |
|---|---|---|---|
| 단독 에이전트 | ~20분 | ~$9 | 핵심 기능 고장 |
| 전체 하네스 (V1) | ~6시간 | ~$200 | 완성도 높은 결과물 |
| 간소화 하네스 (V2) | ~3시간 50분 | ~$124.70 | 2시간+ 일관된 세션 |

## 설치

`harness-design/` 폴더를 `~/.claude/skills/`에 복사합니다.

## 사용법

```
"AI 글쓰기 도구 랜딩페이지 만들어줘"
"차량 관제 대시보드 디자인해줘"
"다크모드 포트폴리오 웹사이트 제작해줘"
```

웹사이트/앱 디자인 요청 시 자동으로 활성화됩니다.

## 라이선스

MIT
