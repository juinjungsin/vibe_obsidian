# 배포 가이드 — vibe_obsidian

## 현재 상태
- 리포지토리 https://github.com/juinjungsin/vibe_obsidian — 빈 상태 (확인 완료)
- 샌드박스에 GitHub 인증 없음 → 사용자 로컬에서 push 실행 필요
- `index.html` 파일 준비 완료 (GitHub Pages 기본 진입 파일명)

## 1. 터미널 명령어 (사용자 로컬 macOS)

```bash
cd ~/Documents/Claude/Projects/바이브옵시디언

# git 초기화
git init -b main
git remote add origin https://github.com/juinjungsin/vibe_obsidian.git

# 파일 추가 및 커밋
git add index.html 바이브옵시디언_강의제안서.html
git commit -m "feat: 바이브 옵시디언 배포"

# 최초 push
git push -u origin main
```

push 시 GitHub 사용자명과 Personal Access Token 입력 (비밀번호 자리에 PAT).

## 2. Personal Access Token 발급

1. https://github.com/settings/tokens → Generate new token (classic)
2. 권한: `repo` 체크
3. 토큰 복사 → push 시 비밀번호 자리에 붙여넣기

## 3. GitHub Pages 활성화

1. https://github.com/juinjungsin/vibe_obsidian/settings/pages 이동
2. Source → `Deploy from a branch`
3. Branch → `main` / Folder → `/ (root)` → Save
4. 1-2분 후 배포 완료

## 4. 배포 URL

```
https://juinjungsin.github.io/vibe_obsidian/
```

## 5. 이후 업데이트

```bash
cd ~/Documents/Claude/Projects/바이브옵시디언
git add index.html
git commit -m "update: 내용 수정"
git push
```
