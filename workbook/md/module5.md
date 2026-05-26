---
module: 5
title: 따라해봅시다 — 자동화
source_html: ../module5.html
page_title: 모듈 5 · 자동화 — 바이브 옵시디언 워크북
purpose: 수정 지시용 원고
---

> 이 파일은 HTML 워크북 내용을 수정 지시용으로 옮긴 원고입니다.
> 내용 수정은 이 Markdown을 기준으로 지시하고, 최종 반영은 원본 HTML에 적용합니다.

Module 5 · Automation

# 따라해봅시다 — 자동화

inbox 폴더 감시(파일 추가 시 자동 변환) + 매일 정해진 시각 스케줄 실행(중복 검사, 백링크 제안)을 설정한다. 2개 자동화 시나리오를 본인 Vault에 적용한다.

소요 **25분**(실측 23.6분, 5분 단위)
방식 **설정 + 트리거 확인**
완료 시점 **자동 변환 1건 + 스케줄 1건**

**LEARNING GOALS**

1. **폴더 감시 자동화**를 통해 raw 1건이 wiki 1건으로 변환되는 흐름을 만든다.
2. **스케줄 자동화**로 매일 정해진 시각에 1개 작업이 실행되도록 한다.
3. 자동화 **로그**를 어디서 보는지 확인한다.

01 / FLOW

## 자동화 흐름 (2 시나리오)

파일 감시 · 스케줄

> [시각 자료] 자동화 흐름도 - ▲ A) inbox 파일 감시 · B) 스케줄 기반 일일 스캔

02 / WATCH

## A. 폴더 감시 설정

Claudian → Automations

Claudian Settings → Automations → New rule.

```
rule: auto-summarize-inbox
trigger:
type: file_create
path: 00_inbox/**/*.md
action:
- run: claudian.summarize
output: 20_wiki/{filename_slug}.md
- run: claudian.suggest_links
limit: 5
debounce: 3s   # 같은 파일 연속 저장 방지
on_error: log  # 또는 notify
```

| 필드 | 본인 입력 |
| --- | --- |
| rule name | [작성] |
| trigger path | [작성] |
| action 1 | [작성] |
| action 2 (선택) | [작성] |
| debounce 초 | [작성] |

### 동작 확인 절차

- `00_inbox/`에 새 노트 `test-watch.md` 작성 후 저장
- 3~10초 대기 → `20_wiki/test-watch.md` 생성 확인
- Claudian 로그 패널에서 **summarize · suggest\_links** 실행 기록 확인

03 / SCHEDULE

## B. 스케줄 설정 (매일 09:00)

Cron 형식

```
rule: daily-wiki-scan
trigger:
type: schedule
cron: "0 9 * * *"   # 매일 09:00
action:
- run: claudian.dedupe
folder: 20_wiki
threshold: 0.85
- run: claudian.suggest_links
folder: 20_wiki
limit_per_note: 3
- run: claudian.report
output: 99_meta/daily_report_{YYYY-MM-DD}.md
```

### Cron 표기 예시

| 식 | 의미 |
| --- | --- |
| `0 9 * * *` | 매일 09:00 |
| `0 9 * * 1-5` | 평일 09:00 |
| `0 */6 * * *` | 6시간마다 |
| `30 22 * * 0` | 매주 일요일 22:30 |

> [작성칸] 본인이 적용하고 싶은 스케줄 표기 (Cron)

04 / LOG

## 자동화 로그 확인

디버깅 기본 경로

| 로그 위치 | 내용 |
| --- | --- |
| `99_meta/claudian.log` | 모든 실행 이력 (timestamp · rule · result · 소요 ms) |
| Claudian 패널 → Log 탭 | 최근 50건 실시간 표시 |
| Obsidian Developer Tools (Cmd/Ctrl+Shift+I) | 오류 stack trace |

```
# claudian.log 샘플
2026-05-18 09:00:01 [INFO]  daily-wiki-scan started
2026-05-18 09:00:08 [INFO]  dedupe scanned=47 candidates=3
2026-05-18 09:00:14 [INFO]  suggest_links generated=12 (4 notes touched)
2026-05-18 09:00:15 [INFO]  report saved 99_meta/daily_report_2026-05-18.md
2026-05-18 09:00:15 [DONE]  duration=14.2s tokens_used=3,418
```

05 / DESIGN

## 본인 자동화 1건 설계

설계 → 실행 → 확인

| 설계 항목 | 본인 입력 |
| --- | --- |
| 자동화 이름 | [작성] |
| 트리거 (감시/스케줄) | [작성] |
| 대상 폴더 / 파일 패턴 | [작성] |
| 실행 액션 1 | [작성] |
| 실행 액션 2 (선택) | [작성] |
| 출력 위치 | [작성] |
| 실패 시 대응 | [작성] |

> [메모 영역] 설계한 자동화를 실행한 결과 기록 (실행 시각 · 성공 여부 · 처리된 노트 수 · 소요 시간)

### 모듈 5 종료 시 셀프 체크

- **폴더 감시 규칙 1건**이 활성화되어 있다.
- inbox에 테스트 파일을 넣었을 때 **wiki에 자동 생성**되는 것을 확인했다.
- **스케줄 규칙 1건**이 등록되어 있다.
- `99_meta/claudian.log` 또는 Log 탭에서 **실행 이력**을 봤다.
