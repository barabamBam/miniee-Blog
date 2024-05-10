# Git 🆚 GitHub❔

Git: 분산버전관리시스템, 버전을 관리하기 위한 **SW 프로그램**

GitHub: 분산버전관리를 위한 **웹사이트**

- 회사에서 사용하고 있는 GitHub
    1. 이슈생성
    2. GitHub Project
    3. 깃헙 위키
- [github.com](http://github.com) → [github.dev](http://github.dev) 로 바꾸면 code 입력 가능!
- .gitignore을 이용하여 민감한 데이터는 숨기기 → **[gitingnore.io](http://gitingnore.io)** 사용하면 아주 편함 😆

⭐ README 중요

⭐ 머메이드 mermaid로 작성할 것❗

[Online FlowChart & Diagrams Editor - Mermaid Live Editor](https://mermaid.live/)

- ERD(데이터베이스 모델링), WBS(개발 일정)

# CLI

(이 외에도 명령어는 있으나 주로 쓰는 명령어는 아래와 같다, git 명령어는 위에서부터 순서대로 씀)

| 명령어 | 설명 |
| --- | --- |
| git clone 주소 | 프로젝트 가져오기 |
| git pull | 프로젝트 내 수정사항 가져오기 |
| git add .  | 모든 수정사항 임시등록       // 점은 every를 의미 |
| git push | 수정사항 올리기 |
| git commit -m “메세지” | 수정사항을 올릴 때 멘트, 어떠한 부분을 수정하였는지를 입력 |
| touch 파일 이름 | 파일 생성 |

사용 예시

```bash
# ex) 개인 GitHub Project가 만들어진 상태에서 내용 추가 및 수정
git clone 주소 . # 뒤에 점은 새로운 디렉터리 미생성을 의미
git add .
git commit -m "메세지"
git push

# ex) 팀 GitHub Project가 만들어진 상태에서 내용 추가 및 수정
🙆🙆‍♂️ git clone 주소
🙆 내용 수정
		git add .
		git commit -m "🙆 modify"
		git push
🙆‍♂️ git pull
		내용 수정
		git add .
		git commit -m "🙆‍♂️ modify"
		git push
```

---

## 추가 git 명령어

| 명령어 | 옵션 | 설명 |
| --- | --- | --- |
| git config --global | init.defaultbBranch main

user.email "이메일"
http://user.name/ 깃허브 아이디 | 기본 브랜치를 main으로 설정

GitHub 연결 |
| git config | —list
—get user. | 연결 상태 확인
연결된 계정 확인 |
| git init |  | 로컬에 레포지토리 연결 |
| git status |  | 파일 상태 확인 |
| git diff |  | add 명령어 사용 전 최근 커밋과의 변경사항 확인 |
| git log |  | 커밋 히스토리 조회 |
- git status에서 상태의 의미
    - Untracked: 한번도 `git add` 하지 않은 상태
    - Tracked: git이 관리 중인 파일
        - Unmodified: 최근 커밋과 변화 ❌
        - Modified: 최근 커밋과 변화 ⭕
        - Staged: `git add` 상태(즉, 수정됨)


# Gitmoji❤️

```
🎨:art:
코드의 구조/형태 개선
Improve structure / format of the code.

⚡️:zap:
성능 개선
Improve performance.

🔥:fire:
코드/파일 삭제
Remove code or files.

🐛:bug:
버그 수정
Fix a bug.

🚑:ambulance:
긴급 수정
Critical hotfix.

✨:sparkles:
새 기능
Introduce new features.

📝:memo:
문서 추가/수정
Add or update documentation.

💄:lipstick:
UI/스타일 파일 추가/수정
Add or update the UI and style files.

🎉:tada:
프로젝트 시작
Begin a project.

✅:white_check_mark:
테스트 추가/수정
Add or update tests.

🔒:lock:
보안 이슈 수정
Fix security issues.

🔖:bookmark:
릴리즈/버전 태그
Release / Version tags.

💚:green_heart:
CI 빌드 수정
Fix CI Build.

📌:pushpin:
특정 버전 의존성 고정
Pin dependencies to specific versions.

👷:construction_worker:
CI 빌드 시스템 추가/수정
Add or update CI build system.

📈:chart_with_upwards_trend:
분석, 추적 코드 추가/수정
Add or update analytics or track code.

♻️:recycle:
코드 리팩토링
Refactor code.

➕:heavy_plus_sign:
의존성 추가
Add a dependency.

➖:heavy_minus_sign:
의존성 제거
Remove a dependency.

🔧:wrench:
구성 파일 추가/삭제
Add or update configuration files.

🔨:hammer:
개발 스크립트 추가/수정
Add or update development scripts.

🌐:globe_with_meridians:
국제화/현지화
Internationalization and localization.

💩:poop:
똥싼 코드
Write bad code that needs to be improved.

⏪:rewind:
변경 내용 되돌리기
Revert changes.

🔀:twisted_rightwards_arrows:
브랜치 합병
Merge branches.

📦:package:
컴파일된 파일 추가/수정
Add or update compiled files or packages.

👽:alien:
외부 API 변화로 인한 수정
Update code due to external API changes.

🚚:truck:
리소스 이동, 이름 변경
Move or rename resources (e.g.: files paths routes).

📄:page_facing_up:
라이센스 추가/수정
Add or update license.

💡:bulb:
주석 추가/수정
Add or update comments in source code.

🍻:beers:
술 취해서 쓴 코드
Write code drunkenly.

🗃:card_file_box:
데이버베이스 관련 수정
Perform database related changes.

🔊:loud_sound:
로그 추가/수정
Add or update logs.

🙈:see_no_evil:
.gitignore 추가/수정
Add or update a .gitignore file.
```