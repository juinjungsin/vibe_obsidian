---
module: etc1
title: 자동화 규칙 설계 메모
source_html: ../module5.html
page_title: etc1 · 자동화 규칙 설계 메모 — 바이브 옵시디언 워크북
purpose: 수정 지시용 원고
---

> 이 파일은 HTML 워크북 내용을 수정 지시용으로 옮긴 원고입니다.
> 내용 수정은 이 Markdown을 기준으로 지시하고, 최종 반영은 원본 HTML에 적용합니다.

# 자동화 규칙 설계 메모

## 1. 질문: ingest 폴더 자동 노트화

특정 폴더를 ingest 폴더로 지정하고, 특정 시간에 해당 폴더에 파일이 있는지 감지하고 있다면 파일을 Markdown 문법에 맞게 작성하여 노트로 등록하는 자동화를 만들고 싶다.

초안 프롬프트:

```yaml
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

## 2. ingest 폴더 자동화 초안 평가

초안은 방향은 좋지만, 실제 요구사항인 **“특정 시간에 ingest 폴더를 확인하고 파일이 있으면 Markdown 노트로 등록”**하는 방식과는 조금 다르다.

현재 초안의 핵심 동작은 다음에 가깝다.

- `file_create` 이벤트가 발생하면 실행
- `00_inbox/**/*.md` 안에 새 Markdown 파일이 생기면 요약
- 결과를 `20_wiki/{filename_slug}.md`로 생성
- 관련 링크를 최대 5개 제안
- 같은 파일 연속 저장 방지를 위해 debounce 적용
- 오류는 로그 또는 알림 처리

하지만 이 구조는 **스케줄 기반 자동화**가 아니라 **이벤트 기반 자동화**다.

## 3. ingest 폴더 자동화에서 보완할 점

명확히 해야 할 조건은 다음과 같다.

1. ingest 폴더가 정확히 어디인지
2. 대상 파일 형식이 `.md`만인지, `.txt`, `.pdf`, `.docx`, `.html` 등도 포함하는지
3. 원본 파일을 변환하는지, 요약하는지, 정리하는지
4. 이미 처리한 파일을 다시 처리하지 않을 기준
5. 처리 후 원본 파일을 어디로 이동할지
6. 생성될 노트의 frontmatter 형식
7. 기존 같은 이름의 노트가 있을 때 덮어쓸지, 새 이름을 만들지
8. 관련 링크 제안 결과를 본문에 넣을지, 별도 메타데이터로 둘지

## 4. ingest 폴더 자동화 개선 예시

```yaml
rule: scheduled-ingest-to-note

description: >
  매일 지정된 시간에 ingest 폴더를 확인하고,
  새 파일이 있으면 Obsidian Markdown 문법에 맞는 노트로 변환하여 등록한다.

trigger:
  type: schedule
  cron: "0 9 * * *"
  timezone: "Asia/Seoul"

source:
  path: "00_inbox/ingest"
  include:
    - "*.md"
    - "*.txt"
    - "*.pdf"
    - "*.docx"
  recursive: true

condition:
  if_empty: noop
  skip_already_processed: true

action:
  - run: claudian.ingest_to_markdown
    output: "20_wiki/{date}-{filename_slug}.md"
    options:
      language: "ko"
      preserve_original_content: true
      summarize: true
      format: obsidian_markdown
      add_frontmatter: true
      frontmatter:
        source_file: "{source_path}"
        created: "{now}"
        status: "ingested"
        tags:
          - inbox
          - auto-ingested

  - run: claudian.suggest_links
    limit: 5
    write_to: "related_notes_section"

  - run: move_file
    to: "00_inbox/processed/{filename}"

on_duplicate:
  strategy: append_number

on_error:
  strategy: log
  log_path: "99_system/logs/ingest-errors.md"
```

## 5. 핵심 수정 포인트

### 5.1 `file_create` 대신 `schedule`

특정 시간에 폴더를 확인하려면 다음처럼 작성하는 것이 더 정확하다.

```yaml
trigger:
  type: schedule
  cron: "0 9 * * *"
```

예: 매일 오전 9시 실행.

### 5.2 ingest 폴더와 출력 노트 폴더 분리

```yaml
source:
  path: "00_inbox/ingest"

output:
  path: "20_wiki"
```

이렇게 해야 “대기 중인 파일”과 “정식 노트”가 섞이지 않는다.

### 5.3 중복 처리 방지

자동화에서 가장 중요한 부분 중 하나다.

```yaml
skip_already_processed: true
```

또는 처리 후 원본을 이동한다.

```yaml
move_file:
  to: "00_inbox/processed"
```

이 조건이 없으면 같은 파일이 매일 반복 처리될 수 있다.

### 5.4 Markdown 노트 템플릿 명시

생성될 노트 형식을 명확히 지정하면 더 안정적이다.

```markdown
---
title: "{title}"
source: "{source_path}"
created: "{now}"
tags:
  - inbox
  - auto-ingested
---

# {title}

## 요약

{summary}

## 원문 정리

{cleaned_content}

## 관련 노트

{suggested_links}
```

## 6. ingest 자동화 자연어 요청 예시

```text
00_inbox/ingest 폴더를 매일 오전 9시에 확인해서 새 파일이 있으면 Obsidian Markdown 노트로 변환해줘.

.md, .txt, .pdf, .docx 파일을 대상으로 하고, 결과는 20_wiki 폴더에 {날짜}-{파일명}.md 형식으로 저장해줘.

생성된 노트에는 YAML frontmatter, 요약, 정리된 본문, 관련 노트 링크 5개를 포함해줘.

처리 완료된 원본 파일은 00_inbox/processed로 이동하고, 이미 처리한 파일은 다시 처리하지 않게 해줘.

오류는 99_system/logs/ingest-errors.md에 기록해줘.
```

---

# 논문 검색 자동화 설계

## 7. 질문: 특정 사이트에서 키워드 검색 후 논문 노트화

특정 사이트, 예를 들어 논문 조회 사이트에서 특정 키워드를 매일 같은 시간에 검색하고, 논문 내용을 읽고 노트로 등록하는 작업을 매일 수행한다면 어떤 조건으로 수행해야 할지 확인하고 싶다.

## 8. 논문 검색 자동화의 전체 흐름

이 자동화는 단순 파일 감지보다 조건이 더 많다.

핵심 흐름은 다음과 같다.

1. 정해진 시간에 실행
2. 특정 논문 사이트 또는 API에서 키워드 검색
3. 신규 논문인지 판별
4. 본문 또는 초록 접근 가능 여부 확인
5. 논문 내용 읽기
6. 요약 및 정리
7. Obsidian Markdown 노트 생성
8. 관련 노트 링크 추천
9. 처리 상태 기록
10. 오류 기록

## 9. 실행 조건

```yaml
trigger:
  type: schedule
  cron: "0 8 * * *"
  timezone: "Asia/Seoul"
```

의미: 매일 오전 8시에 실행.

정해야 할 것:

- 매일 몇 시에 실행할지
- 주말도 실행할지
- 실패하면 재시도할지
- 마지막 실행 이후 새 논문만 볼지
- 매번 최근 24시간을 볼지

## 10. 검색 대상 사이트 조건

예시:

```yaml
source:
  name: arxiv
  type: api
  endpoint: "arxiv_api"
```

정해야 할 것:

- 대상 사이트: arXiv, PubMed, Semantic Scholar, Google Scholar, 특정 학회 사이트 등
- 공식 API 또는 RSS가 있는지
- 로그인이 필요한지
- PDF 다운로드가 허용되는지
- 자동화 접근이 사이트 이용약관이나 robots.txt에 위반되지 않는지

주의할 점:

- Google Scholar처럼 자동화 크롤링에 제한이 있는 사이트는 주의해야 한다.
- 가능하면 공식 API, RSS, OpenAlex, Crossref, arXiv API, PubMed API 같은 경로를 사용하는 것이 안전하다.

## 11. 검색 키워드 조건

단순 예시:

```yaml
query:
  keywords:
    - "site reliability engineering"
    - "incident response"
    - "root cause analysis"
  operator: OR
```

더 정확한 예시:

```yaml
query:
  expression: '("site reliability engineering" OR SRE) AND ("incident response" OR "root cause analysis")'
  fields:
    - title
    - abstract
    - keywords
```

정해야 할 것:

- 제목만 검색할지
- 초록까지 검색할지
- 본문까지 검색할지
- AND / OR 조건을 어떻게 구성할지
- 제외 키워드를 둘지

제외 키워드 예시:

```yaml
exclude:
  keywords:
    - "marketing"
    - "survey only"
```

## 12. 수집 대상 필터 조건

```yaml
filter:
  published_since: last_successful_run
  language:
    - en
    - ko
  max_results: 10
  require_abstract: true
  require_open_access: true
```

정해야 할 것:

- 하루 최대 몇 편까지 처리할지
- Open Access 논문만 읽을지
- 초록만 읽고 노트화할지
- PDF 본문까지 읽을지
- preprint를 포함할지
- peer-reviewed 논문만 포함할지

## 13. 중복 방지 조건

자동화에서 매우 중요하다.

```yaml
dedupe:
  keys:
    - doi
    - arxiv_id
    - url
    - title_hash
  state_file: "99_system/automation/paper-ingest-state.json"
```

중복 기준 후보:

- DOI
- arXiv ID
- PubMed ID
- URL
- 제목 해시
- 이미 생성된 노트의 frontmatter `paper_id`

## 14. 읽기 범위 조건

```yaml
read:
  priority:
    - abstract
    - conclusion
    - full_text_pdf
  full_text: open_access_only
```

정해야 할 것:

- 초록만 요약할지
- PDF 전체를 읽을지
- Introduction / Method / Results / Conclusion 중심으로 읽을지
- 표와 그림 캡션도 읽을지
- 참고문헌은 제외할지

## 15. 노트 생성 조건

Obsidian 노트로 등록하려면 템플릿이 명확해야 한다.

```yaml
output:
  folder: "20_wiki/papers"
  filename: "{published_date}-{title_slug}.md"
  format: obsidian_markdown
```

추천 노트 구조:

```markdown
---
title: "{paper_title}"
authors:
  - "{author_1}"
published: "{published_date}"
source: "{source_name}"
url: "{paper_url}"
doi: "{doi}"
paper_id: "{paper_id}"
tags:
  - paper
  - research
  - auto-ingested
status: unread
created: "{now}"
---

# {paper_title}

## 한 줄 요약

{one_sentence_summary}

## 핵심 내용

{summary}

## 문제의식

{research_problem}

## 방법론

{method}

## 주요 결과

{findings}

## SRE/RCA 관점에서의 시사점

{implications}

## 인용할 만한 개념

{key_concepts}

## 관련 노트

{suggested_links}

## 원문

- [논문 링크]({paper_url})
- DOI: `{doi}`
```

## 16. 관련 링크 추천 조건

```yaml
linking:
  suggest_links: true
  limit: 5
  target_folders:
    - "20_wiki"
    - "30_areas"
```

정해야 할 것:

- 관련 노트 몇 개까지 추천할지
- 자동으로 링크를 삽입할지
- 후보만 남길지
- 어떤 폴더를 검색 대상으로 삼을지

## 17. 오류 처리 조건

```yaml
on_error:
  strategy: log_and_continue
  log_path: "99_system/logs/paper-ingest-errors.md"
```

필요한 오류 조건:

- 사이트 접속 실패
- API 제한 초과
- PDF 다운로드 실패
- 본문 추출 실패
- 중복 파일명 발생
- 요약 실패
- 노트 저장 실패

## 18. 논문 검색 자동화 개선 예시

```yaml
rule: daily-paper-research-ingest

description: >
  매일 오전 8시에 지정한 논문 검색 사이트에서 키워드를 검색하고,
  새 논문이 있으면 초록 또는 공개 본문을 읽어 Obsidian Markdown 노트로 등록한다.

trigger:
  type: schedule
  cron: "0 8 * * *"
  timezone: "Asia/Seoul"

source:
  name: arxiv
  access: api
  search_scope:
    - title
    - abstract

query:
  expression: '("site reliability engineering" OR SRE) AND ("incident response" OR "root cause analysis")'
  exclude_keywords:
    - "marketing"
  max_results: 10

filter:
  published_since: last_successful_run
  require_abstract: true
  require_open_access: false
  language:
    - en
    - ko

dedupe:
  keys:
    - doi
    - arxiv_id
    - url
    - title_hash
  state_file: "99_system/automation/paper-ingest-state.json"

read:
  use:
    - title
    - abstract
    - conclusion
  full_text_pdf: open_access_only

action:
  - run: claudian.paper_summarize
    output: "20_wiki/papers/{published_date}-{title_slug}.md"
    template:
      frontmatter: true
      sections:
        - 한 줄 요약
        - 핵심 내용
        - 문제의식
        - 방법론
        - 주요 결과
        - SRE/RCA 관점에서의 시사점
        - 관련 노트
        - 원문 링크

  - run: claudian.suggest_links
    limit: 5
    target_folders:
      - "20_wiki"
      - "30_areas"

on_empty: log
on_duplicate: skip
on_error:
  strategy: log_and_continue
  log_path: "99_system/logs/paper-ingest-errors.md"
```

## 19. 논문 검색 자동화 자연어 요청 예시

```text
매일 오전 8시에 arXiv에서 "site reliability engineering" OR SRE 와 "incident response" OR "root cause analysis" 관련 논문을 검색해줘.

마지막 성공 실행 이후 새로 등록된 논문만 대상으로 하고, DOI/arXiv ID/URL/제목 해시를 기준으로 중복은 제외해줘.

각 논문의 제목, 저자, 발행일, URL, 초록을 읽고 가능하면 공개 PDF의 결론까지 참고해서 Obsidian Markdown 노트로 20_wiki/papers에 저장해줘.

노트에는 YAML frontmatter, 한 줄 요약, 핵심 내용, 방법론, 주요 결과, SRE/RCA 관점 시사점, 관련 노트 링크 5개를 포함해줘.

실패나 빈 결과는 99_system/logs/paper-ingest-errors.md에 기록해줘.
```

## 20. 핵심 결론

ingest 폴더 자동화와 논문 검색 자동화 모두 안정적으로 만들기 위해서는 다음 조건이 반드시 필요하다.

1. 실행 조건
   - 파일 생성 이벤트인지
   - 정해진 시간에 실행하는 스케줄인지

2. 입력 조건
   - 어떤 폴더 또는 사이트를 대상으로 할지
   - 어떤 파일 형식 또는 검색 키워드를 대상으로 할지

3. 필터 조건
   - 새 항목만 처리할지
   - 최대 처리 개수
   - 언어
   - Open Access 여부
   - 필수 메타데이터 여부

4. 중복 방지 조건
   - 파일 해시
   - DOI
   - URL
   - 제목 해시
   - 처리 상태 파일

5. 읽기 및 변환 조건
   - 원문 전체를 읽을지
   - 초록만 읽을지
   - 요약할지
   - 원문을 보존할지

6. 출력 조건
   - 저장 폴더
   - 파일명 규칙
   - Obsidian Markdown 형식
   - YAML frontmatter
   - 본문 섹션 구조

7. 연결 조건
   - 관련 노트 추천 여부
   - 추천 개수
   - 링크 삽입 방식
   - 검색 대상 폴더

8. 사후 처리 조건
   - 원본 파일 이동
   - 처리 완료 표시
   - 중복 항목 skip
   - 실패 로그 기록

9. 오류 처리 조건
   - 실패 시 계속 진행할지
   - 알림을 보낼지
   - 로그 파일 위치
   - 재시도 여부

결국 좋은 자동화 요청은 단순히 “검색해서 노트로 만들어줘”가 아니라,
**언제, 어디서, 무엇을, 어떤 기준으로, 어떻게 읽고, 어떤 형식으로, 어디에 저장하고, 실패하면 어떻게 할지**를 명확히 지정해야 한다.
