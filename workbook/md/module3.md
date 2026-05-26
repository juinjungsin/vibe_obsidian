---
module: 3
title: 따라해봅시다 — 설치
source_html: ../module3.html
page_title: 모듈 3 · 설치 — 바이브 옵시디언 워크북
purpose: 수정 지시용 원고
---

> 이 파일은 HTML 워크북 내용을 수정 지시용으로 옮긴 원고입니다.
> 내용 수정은 이 Markdown을 기준으로 지시하고, 최종 반영은 원본 HTML에 적용합니다.

Module 3 · Installation

# 따라해봅시다 — 설치

Obsidian 본체 설치 → Vault 생성 → 커뮤니티 플러그인 활성화 → Claudian 설치 → 동작 확인. 5단계.

소요 **50분**(실측 50.0분, 5분 단위)
방식 **강사 시연 + 동시 실습**
완료 시점 **Claudian 호출 응답 1건**

**LEARNING GOALS**

1. 본인 노트북에 **Obsidian + Claudian**이 동작 가능한 상태로 설치된다.
2. **Vault 폴더 위치**를 본인이 기억할 수 있는 경로에 둔다.
3. Claudian에서 **API 키**가 정상 연결되어 첫 호출이 성공한다.

01 / STEPS

## 설치 5단계

총 35분

1. Obsidian 다운로드 · 설치 5분

`obsidian.md` 접속 → 본인 OS용 설치 파일 다운로드 → 실행 → 기본 옵션으로 설치 진행.
2. Vault 생성 5분

Obsidian 첫 실행 화면에서 **Create new vault** → 이름 `vibe_2026` → 위치 선택. iCloud / OneDrive 동기화 폴더는 비권장.
3. 커뮤니티 플러그인 활성화 5분

Settings → Community plugins → **Restricted mode OFF** → Browse 가능 상태로 진입.
4. Claudian 설치 8분

Browse → 검색 `Claudian` → Install → Enable. 사이드바에 보라색 아이콘 노출 확인.
5. 동작 확인 4분

새 노트 작성 → `Cmd/Ctrl + P` → `Claudian: Summarize` → 응답 1건 수신.

02 / VAULT PATH

## Vault 위치 선택

동기화 충돌 회피

| 위치 유형 | 예시 경로 | 권장 | 비고 |
| --- | --- | --- | --- |
| 로컬 일반 폴더 | `~/Documents/vibe_2026` | 권장 | 기본 선택 |
| iCloud Drive | `~/Library/Mobile Documents/.../vibe` | 주의 | 지연 동기화로 파일 충돌 발생 |
| OneDrive / Dropbox | `~/OneDrive/vibe` | 주의 | 플러그인 설정 파일 잠금 가능 |
| Obsidian Sync | 유료 옵션 | 권장 | 다기기 사용 시 |
| 네트워크 드라이브 | `\\nas\vault` | 비권장 | I/O 지연 큼 |

> [작성칸] 본인이 선택한 Vault 경로 (한 줄로 적기)

03 / FOLDERS

## 최초 폴더 구조 만들기

실습

Vault 생성 직후 다음 5개 폴더를 만든다. 이름·번호는 강사 기본값을 따른다.

```
📁 vibe_2026/
├─ 00_inbox/      # raw 수집
├─ 10_atomic/     # 단일 개념 노트
├─ 20_wiki/       # 정제된 위키
├─ 30_publish/    # 외부 공유 후보
└─ 99_meta/       # 템플릿 · 스크립트
```

- 좌측 파일 트리에서 우클릭 → **New folder** → 위 5개 이름 그대로 생성
- 각 폴더 안에 `.gitkeep` 또는 빈 노트 `readme.md` 1개 추가 (선택)
- Settings → Files & Links → **Default location for new notes** = `00_inbox` 지정

04 / FIRST CALL

## Claudian 첫 호출 확인

동작 검증

```
# 새 노트에 다음 내용을 붙여넣고 Cmd/Ctrl+P → Claudian: Summarize

제목: 바이브 옵시디언 첫 테스트
본문:
PKM은 개인지식관리체계의 약자다. Obsidian은 마크다운 기반 노트
앱으로, 노트 간 백링크와 그래프 뷰를 제공한다. 커뮤니티 플러그인
Claudian을 통해 LLM 호출을 노트 안에서 직접 트리거할 수 있다.

# 예상 응답 (예시)
→ PKM은 개인지식관리체계. Obsidian은 마크다운 노트앱으로 그래프 뷰를 제공.
Claudian으로 노트 안에서 LLM 호출이 가능.
키워드: PKM · Obsidian · Claudian
```

| 관찰 항목 | 본인 결과 |
| --- | --- |
| 응답까지 소요 시간 (초) | [작성] |
| 응답 언어 (한/영) | [작성] |
| 응답 길이 (대략 줄 수) | [작성] |
| 오류 메시지 (있다면 그대로) | [작성] |

### 모듈 3 종료 시 셀프 체크

- Obsidian이 실행되고 **Vault 이름**이 화면 상단에 보인다.
- 좌측 파일 트리에 **5개 폴더**가 만들어졌다.
- 설정 → Community plugins에서 **Claudian**이 Enabled 상태다.
- Claudian 설정 패널의 상태 표시기가 **green**이다.
- **Summarize 명령**으로 응답 1건을 받았다.
