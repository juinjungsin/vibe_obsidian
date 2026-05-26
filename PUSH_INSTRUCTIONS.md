# 워크북 push 명령

샌드박스 권한 제약으로 자동 commit/push가 차단됨. 로컬 터미널에서 아래 명령을 순서대로 실행.

## 1) 폴더로 이동
```bash
cd "/Users/jongbin/Documents/Claude/Projects/바이브옵시디언"
```

## 2) lock 파일 정리 (있을 때만)
```bash
rm -f .git/index.lock
```

## 3) 변경 사항 확인
```bash
git status
```
예상 출력:
- modified:   index.html
- new file:   workbook/_shared.css
- new file:   workbook/index.html
- new file:   workbook/module1.html ~ module7.html
- (선택) new file: DEPLOY.md, PUSH_INSTRUCTIONS.md

## 4) add → commit → push
```bash
git add index.html workbook/ DEPLOY.md PUSH_INSTRUCTIONS.md
git commit -m "Add 7-module workbook (HTML) and link from main proposal

- workbook/index.html: hub page with 7 module cards + 4h timeline
- workbook/module1~7.html: per-module workbooks (235~355 lines each)
- workbook/_shared.css: common dark/purple Obsidian style
- index.html: insert per-module workbook links + TOC entry + hub button"
git push origin main
```

## 5) 배포 확인 (1~2분 후)
- 본 페이지: https://juinjungsin.github.io/vibe_obsidian/
- 워크북 허브: https://juinjungsin.github.io/vibe_obsidian/workbook/
- 모듈 1~7: https://juinjungsin.github.io/vibe_obsidian/workbook/module1.html ~ module7.html
- GitHub Actions 진행 상황: https://github.com/juinjungsin/vibe_obsidian/actions
