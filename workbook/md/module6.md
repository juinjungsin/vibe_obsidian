---
title: 대량의 파일을 MD로 변환하기
source_html: ../module6.html
page_title: M6 · 대량의 파일을 MD로 변환하기 — 바이브 옵시디언 워크북
---

Module 6 · MD Ingest

# 대량의 파일을 MD로 변환하기

MD Maker를 이용해 Obsidian에 넣기 전 다양한 문서 파일을 로컬 PC에서 Markdown으로 일괄 변환한다. ingest 폴더 기반으로 원본을 유지하고 md/csv/pdf 결과와 로그를 확인한다.

- 소요: 35분
- 방식: 도구 구조 이해 + 폴더 실습
- 완료 시점: ingest → md 변환 결과 1건

## Learning Goals

1. Obsidian에 넣기 전 **문서 전처리**가 필요한 이유를 설명한다.
2. `ingest/`, `md/`, `failed/`, `log/` 폴더의 역할을 구분한다.
3. 파일 1~3건을 **Markdown**으로 변환하고 결과와 로그를 확인한다.

## 왜 MD 변환이 필요한가

Obsidian은 Markdown 기반으로 지식 구조를 만들 때 가장 안정적이다. PDF, Word, PPT, Excel, HWP처럼 형식이 섞인 자료는 먼저 텍스트와 구조를 꺼내 `.md`로 정리해야 검색, 백링크, 그래프 뷰, AI 요약 흐름에 자연스럽게 들어온다.

| 기준 | 설명 |
|---|---|
| 원본 보존 | 원본 파일은 `ingest/`에 둔 상태로 결과 파일을 별도 폴더에 만든다. |
| 로컬 처리 | 민감한 자료를 외부 서비스에 올리지 않고 내 PC에서 변환 흐름을 통제한다. |
| Obsidian 입력 | 결과 Markdown을 Vault의 raw 또는 wiki 작성 흐름에 투입한다. |

## 지원 파일과 출력 형식

| 구분 | 형식 | 수업에서 확인할 점 |
|---|---|---|
| 텍스트/웹 | `.txt`, `.html`, `.htm` | 본문 텍스트가 Markdown으로 자연스럽게 옮겨지는지 확인 |
| Office | `.docx`, `.pptx`, `.xlsx`, `.doc`, `.ppt`, `.xls` | 문단, 슬라이드, 표가 읽을 수 있는 구조로 추출되는지 확인 |
| PDF | `.pdf` | 텍스트 PDF와 스캔 PDF의 결과 차이를 비교 |
| 한글 문서 | `.hwp`, `.hwpx` | 변환 도구 준비 여부와 실패 로그 확인 |
| 출력 | `md`, `csv`, `pdf` | 이번 실습은 Markdown 결과를 중심으로 확인 |

권장 기준: 파일 1개당 50MB 이하. 작은 파일부터 순차 처리하면 실패 원인을 추적하기 쉽다.

## 폴더 구조

| 폴더 | 역할 | 수업 체크 |
|---|---|---|
| `ingest/` | 변환할 원본 파일을 넣는 입력 폴더 | [작성] |
| `md/` | Markdown 결과가 저장되는 폴더 | [작성] |
| `csv/` | 표 데이터 결과가 저장되는 폴더 | [작성] |
| `pdf/` | PDF 출력 결과가 저장되는 폴더 | [작성] |
| `failed/` | 변환 실패 파일과 실패 사유 추적 위치 | [작성] |
| `log/` | 일자별 실행 로그 저장 위치 | [작성] |
| `state/` | 처리 상태와 중복 실행 방지 정보 | [작성] |
| `tools/` | 보조 변환 도구 배치 위치 | [작성] |

## 준비물

| 준비물 | 용도 |
|---|---|
| Windows 10/11 | 기본 실행 환경 |
| Python 3.11 이상 | 앱 실행과 의존성 설치 |
| Tkinter | Windows 데스크톱 UI 실행 |
| LibreOffice | 구형 Office 파일 변환 |
| Tesseract OCR | 스캔 PDF와 이미지 기반 슬라이드 텍스트 추출 |
| `unhwp.exe` | `.hwp`, `.hwpx` 변환 흐름 |

```powershell
git clone https://github.com/juinjungsin/obsidian-md-ingest.git
cd obsidian-md-ingest
python -m pip install -r requirements.txt
```

## 실습 절차

1. 프로그램 실행: `python app.py`로 MD Maker 창을 연다.
2. 파일 넣기: `ingest/` 폴더에 변환할 파일 1~3건을 복사한다.
3. 목록 새로고침: Refresh 또는 Ready 목록을 눌러 변환 대상이 보이는지 확인한다.
4. 변환 실행: Ingest Ready Files를 실행하고 진행률, 현재 파일명, 로그 메시지를 확인한다.
5. 결과 확인: `md/` 폴더에서 Markdown 결과를 열고 제목, 본문, 표, 한글 인코딩을 확인한다.

```powershell
python app.py --headless --formats md
python app.py --headless --dry-run --formats md
```

## 결과와 오류 확인

| 확인 위치 | 정상 기준 | 문제가 있으면 |
|---|---|---|
| `md/` | 원본 파일명 기준의 Markdown 결과가 생성됨 | 파일 크기, 확장자, 권한 확인 |
| `failed/` | 실패 파일이 없거나 실패 사유가 분리되어 있음 | 해당 파일만 단독 재실행 |
| `log/YYYYMMDD변환log.txt` | 시작, 변환, 저장, 완료 로그가 시간순으로 기록됨 | 마지막 ERROR 라인부터 원인 추적 |
| Ready 목록 | 실행 후 처리 대상이 비어 있거나 예상 파일만 남음 | 중복 처리 상태와 `state/` 확인 |

> [메모 영역] 이번 실습에서 변환한 파일명 · 결과 폴더 · 오류 여부를 기록한다.

## M6 종료 시 셀프 체크

- [ ] `ingest/`에 넣은 파일이 `md/` 결과로 생성되는 흐름을 확인했다.
- [ ] 변환 실패 시 `failed/`와 `log/`에서 원인을 찾는 위치를 안다.
- [ ] Markdown 결과 1건을 Obsidian Vault에 넣을 수 있는 상태로 열어봤다.
- [ ] 외부 공유가 필요한 원본과 로컬 처리해야 할 원본을 구분했다.