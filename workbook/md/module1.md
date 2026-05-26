---
module: 1
title: 일단 봅시다
source_html: ../module1.html
page_title: 모듈 1 · 일단 봅시다 — 바이브 옵시디언 워크북
purpose: 수정 지시용 원고
---

> 이 파일은 HTML 워크북 내용을 수정 지시용으로 옮긴 원고입니다.
> 내용 수정은 이 Markdown을 기준으로 지시하고, 최종 반영은 원본 HTML에 적용합니다.

Module 1 · Demonstration

# 일단 봅시다

강사의 완성된 Vault를 화면 위에 그대로 띄워 본다. 폴더 구조, 그래프 뷰, raw 노트, Claudian 호출까지 4개 화면을 차례로 본다.

소요 **45분**(실측 42.7분, 5분 단위)
방식 **강사 시연 / 수강생 관찰**
준비물 **화면 + 노트**

**LEARNING GOALS**

1. 바이브 옵시디언으로 도달 가능한 결과물의 **형태**를 본다.
2. 기존 PKM과 무엇이 다른지 **관찰 단위**로 기록한다.
3. 이후 모듈에서 본인이 만들 결과물의 **목표 이미지**를 잡는다.

01 / FLOW

## 시연 흐름 4단계

총 25분 / 1단계당 5~7분

1. Vault 폴더 구조 보기 5분

강사 Vault의 좌측 파일 트리. 폴더 분류 규칙(00\_inbox → 10\_atomic → 20\_wiki → 30\_publish)을 확인한다.
2. 그래프 뷰 펼치기 7분

노트 간 연결을 시각화. 색상은 폴더 기준, 노드 크기는 백링크 수 기준. 클러스터 3~4개 식별.
3. raw 노트 → wiki 노트 비교 6분

동일 주제의 raw(수집 원문)와 wiki(정제 노트)를 좌우로 띄워 차이를 본다.
4. Claudian 호출 7분

옵시디언 안에서 Claudian 명령으로 `요약` · `링크 제안` · `중복 탐지` 3개 명령 시연.

02 / VAULT

## 시연용 Vault 구조 (예시)

더미 데이터

강사 화면에서 실제로 표시되는 폴더 트리 예시. 5개 최상위 폴더 · 노트 총 312개.

```
📁 vault_2026/
├─ 00_inbox/                      # 수집 원문 (raw)
│   ├─ 2026-05-15_arxiv-clip.md
│   ├─ 2026-05-16_meeting-note.md
│   └─ 2026-05-17_book-highlight.md
├─ 10_atomic/                     # 단일 개념 노트
│   ├─ rag-retrieval.md
│   ├─ context-window.md
│   └─ embedding-dim.md
├─ 20_wiki/                       # 정제된 위키 (Karpathy 패턴)
│   ├─ llm-architecture.md
│   ├─ pkm-workflow.md
│   └─ obsidian-plugins.md
├─ 30_publish/                    # 외부 공유 후보
│   └─ blog-drafts/
├─ 99_meta/                       # 템플릿, 자동화 스크립트
│   ├─ templates/
│   └─ automation/

총 312 notes · 1,847 internal links · 23 orphan
```

03 / GRAPH

## 그래프 뷰 시연 (예시)

더미 데이터 · 4 클러스터

> [시각 자료] 더미 그래프 뷰 - ▲ 시연 화면 캡처 예시 — 4 클러스터 / 19 노드 / orphan 3

04 / RAW vs WIKI

## raw 노트 → wiki 노트 변환 예시

동일 주제 좌우 비교

📥 raw — 00\_inbox/2026-05-15\_arxiv-clip.md

```
# 원문 클립 (수집 그대로)

Title: Retrieval-Augmented Generation
Source: arXiv 2020.11401
Date: 2026-05-15

"... we introduce RAG models where
the parametric memory is a pre-trained
seq2seq model and the non-parametric
memory is a dense vector index of
Wikipedia..."

tags: #raw #unread
```

📚 wiki — 20\_wiki/rag-retrieval.md

```
# 정제된 위키 노트

# RAG (Retrieval-Augmented Generation)

## 한 문장 요약
질문 시점에 외부 인덱스에서
관련 문서를 끌어와 LLM 입력에
붙이는 방식.

## 구성
- parametric: seq2seq 모델
- non-parametric: 벡터 인덱스

## 관련
- [[context-window]]
- [[embedding-dim]]
- [[llm-architecture]]

tags: #wiki #atomic
```

**관찰 포인트**

raw는 출처와 원문이 보존된다. wiki는 한 문장 요약 + 구조 + 백링크가 추가된다. 두 노트는 분리된 상태로 공존한다.

05 / CLAUDIAN

## 옵시디언 안에서 Claudian 호출

3개 명령 시연

| 명령 | 입력 | 출력 형태 | 시간 |
| --- | --- | --- | --- |
| `/summarize` | 00\_inbox/arxiv-clip.md 선택 | 한 문장 요약 + 키워드 3개 + 인용 | ≈ 4s |
| `/link` | 현재 노트와 유사한 wiki 후보 | 백링크 후보 5건 + 유사도 점수 | ≈ 6s |
| `/dedupe` | 20\_wiki 폴더 스캔 | 중복 의심 페어 목록 | ≈ 12s |

06 / OBSERVE

## 시연 관찰 노트 (수강생 작성)

강의 중 작성

강사 화면을 보면서 떠오르는 점을 그대로 적는다. 결론을 내지 않아도 된다.

| 시연 단계 | 본 것 (사실) | 의문점 / 메모 |
| --- | --- | --- |
| Vault 폴더 구조 | [작성] | [작성] |
| 그래프 뷰 | [작성] | [작성] |
| raw → wiki 변환 | [작성] | [작성] |
| Claudian 명령 | [작성] | [작성] |

> [작성칸] 시연 후 가장 먼저 따라 해보고 싶은 것 하나만 적는다.

### 셀프 체크 (모듈 종료 전)

- **4개 시연 단계**가 무엇이었는지 말로 설명할 수 있다.
- 강사 Vault에서 **raw 폴더와 wiki 폴더의 역할 차이**를 한 문장으로 적었다.
- 그래프 뷰에서 본 **클러스터 개수**를 기록했다.
- Claudian 호출 시 **응답 시간 범위**를 봤다 (한 자리 초 ~ 두 자리 초).
