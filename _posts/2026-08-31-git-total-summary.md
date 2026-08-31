---
layout: post
title: "Git 명령어와 commit·push·pull·PR 총정리"
date: 2026-08-31
categories: [Git, 정리]
tags: [git, github, commit, push, pull, pull-request]
mermaid: true
---



## 전체 흐름부터 한눈에 보기

Git은 결국 "내 컴퓨터 안의 작업 공간"에서 "GitHub"까지 파일을 단계별로 옮기는 도구다.
아래 그림이 이번 주에 배운 전체 흐름이다.

<div class="mermaid">
flowchart LR
    A[Working Directory\n지금 수정 중인 파일] -->|git add| B[Staging Area\n커밋할 것만 모아둠]
    B -->|git commit| C[Local Repository\n내 컴퓨터 안의 저장 기록]
    C -->|git push| D[Remote Repository\nGitHub]
    D -->|git pull| C
</div>

- **Working Directory**: 지금 내가 만지고 있는 파일들
- **Staging Area**: `git add`로 "이번 커밋에 넣을 것"만 골라둔 임시 공간
- **Local Repository**: `git commit`으로 실제 저장 기록이 남는 곳 (아직 내 컴퓨터 안에만 있음)
- **Remote Repository**: `git push`로 올려서 GitHub에서 확인할 수 있는 곳

여기서부터 하나씩 뜯어본다.

## 1. 저장소 만들기

새로운 프로젝트를 시작할 때, "이 폴더를 Git으로 관리하겠다"고 선언하는 단계다.

| 명령어 | 언제 쓰나 |
|---|---|
| `git init` | 폴더를 새로 Git 저장소로 만들고 싶을 때 (맨 처음 딱 한 번) |

`git init`을 치면 그 폴더 안에 `.git`이라는 숨김 폴더가 생기는데, 여기에 앞으로의 모든 기록이 저장된다.

## 2. 스테이징

수정한 파일 중에서 "이번엔 이것만 커밋에 담겠다"고 고르는 단계다.

| 명령어 | 언제 쓰나 |
|---|---|
| `git add 파일명` | 특정 파일 하나만 커밋 대상으로 담고 싶을 때 |
| `git add .` | 수정한 파일을 전부 커밋 대상으로 담고 싶을 때 |

파일을 두 개 고쳤는데 하나만 커밋하고 싶은 경우가 있어서, `add`와 `commit`이 나뉘어 있는 것이다.

## 3. commit, push, pull — 기록을 저장하고 옮기기

작업하면서 commit, push, pull을 실제로 써봤다. 각각을 내 말로 정리하면 이렇다.

| 명령어 | 내가 이해한 의미 |
|---|---|
| `git commit` | 저장 — 지금까지 고친 내용을 하나의 기록으로 남기는 것 |
| `git push` | 보내기 — 내 컴퓨터에 있는 기록을 GitHub로 올리는 것 |
| `git pull` | 받기 — GitHub에 있는 최신 기록을 내 컴퓨터로 가져오는 것 |

셋 다 "기록을 어디에, 어느 방향으로 옮기느냐"의 차이라고 생각하니 헷갈리지 않았다.

<div class="mermaid">
flowchart LR
    A[작업 중인 파일] -->|commit: 저장| B[내 컴퓨터의 기록]
    B -->|push: 보내기| C[GitHub]
    C -->|pull: 받기| B
</div>

commit은 아직 내 컴퓨터 안에만 있는 상태이고, push를 해야 GitHub에 올라간다.

| 명령어 | 언제 쓰나 |
|---|---|
| `git commit -m "메시지"` | 스테이징해둔 내용을 하나의 저장 지점(기록)으로 남기고 싶을 때 |
| `git push` | 내 컴퓨터에 쌓인 커밋 기록을 GitHub로 올리고 싶을 때 |
| `git pull` | GitHub에 있는 최신 기록을 내 컴퓨터로 받아오고 싶을 때 |

## 4. 브랜치

브랜치는 원본(main)을 건드리지 않고 독립적으로 작업할 수 있는 별도의 작업 공간이다.

| 명령어 | 언제 쓰나 |
|---|---|
| `git branch 브랜치명` | 새 작업 공간(브랜치)을 하나 만들고 싶을 때 |
| `git checkout 브랜치명` | 만들어둔 브랜치로 옮겨가서 작업하고 싶을 때 |

<div class="mermaid">
gitGraph
    commit id: "main 시작"
    branch feature
    checkout feature
    commit id: "feature 작업1"
    commit id: "feature 작업2"
    checkout main
    merge feature id: "merge!"
</div>

`git branch`로 만들기만 하면 아직 그 브랜치로 옮겨간 게 아니라는 점이 헷갈리기 쉽다. 반드시 `git checkout`으로 이동까지 해줘야 한다.

## 5. merge

merge는 서로 다른 브랜치의 작업 내용을 하나로 합치는 것이다.

| 명령어 | 언제 쓰나 |
|---|---|
| `git merge 브랜치명` | 다른 브랜치에서 작업한 내용을 지금 브랜치로 합치고 싶을 때 |

보통 feature 브랜치에서 작업을 끝낸 뒤, main 브랜치로 이동해서 `git merge feature`를 실행하는 순서로 쓴다.

## 6. 헷갈렸던 부분: PR 만드는 법

commit, push, pull은 전부 명령어라서 터미널에서 바로 실행하면 됐는데, PR(Pull Request)은 명령어가 아니라 GitHub 화면에서 만드는 것이어서 헷갈렸다. "pull"이라는 이름 때문에 `git pull`과 헷갈리기도 했다.

정리해보니 순서는 이랬다.

<div class="mermaid">
sequenceDiagram
    participant Me as 나 (feature 브랜치)
    participant GitHub as GitHub
    participant Main as main 브랜치

    Me->>GitHub: git push (내 브랜치를 올림)
    Me->>GitHub: "New Pull Request" 버튼 클릭
    GitHub-->>Me: 변경 내용 비교 화면 표시
    Me->>GitHub: 제목/설명 작성 후 PR 생성
    GitHub->>Main: (리뷰 후) Merge
</div>

즉 `git pull`은 GitHub → 내 컴퓨터로 코드를 받는 **명령어**이고, PR은 "내 브랜치 내용을 main에 합쳐달라"고 **요청하는 화면 작업**이라는 게 이번에 정리하면서 명확해졌다.

## 7. 되돌리기

작업하다가 실수했을 때, 상황에 따라 되돌리는 방법이 다르다는 걸 배웠다.

| 명령어 | 언제 쓰나 |
|---|---|
| `git restore 파일명` | 아직 커밋 전, 방금 고친 파일을 원래대로 되돌리고 싶을 때 |
| `git reset 커밋` | 이미 한 커밋을 취소하고 그 이전 상태로 되돌리고 싶을 때 |

<div class="mermaid">
flowchart TD
    Q{아직 커밋을 안 했나?} -->|응, 커밋 전| A[git restore 파일명]
    Q -->|아니, 이미 커밋함| B[git reset 커밋]
</div>

즉, "커밋 전이냐 후냐"만 구분하면 어떤 명령어를 써야 할지 헷갈리지 않는다.

## 오늘의 명령어 총정리

| 명령어 | 언제 쓰나 |
|---|---|
| `git init` | 폴더를 새 Git 저장소로 만들고 싶을 때 |
| `git add` | 커밋에 담을 파일을 고르고 싶을 때 |
| `git commit` | 고른 파일들을 하나의 기록으로 남기고 싶을 때 |
| `git branch` | 새로운 작업 공간(브랜치)을 만들고 싶을 때 |
| `git checkout` | 다른 브랜치로 옮겨가고 싶을 때 |
| `git push` | 내 기록을 GitHub로 올리고 싶을 때 |
| `git pull` | GitHub의 최신 기록을 받아오고 싶을 때 |
| `git merge` | 다른 브랜치의 작업을 지금 브랜치로 합치고 싶을 때 |
| `git restore` | 커밋 전 파일을 원래대로 되돌리고 싶을 때 |
| `git reset` | 이미 한 커밋을 취소하고 싶을 때 |
| PR (GitHub 화면) | 내 브랜치 내용을 main에 합쳐달라고 요청하고 싶을 때 |

## 더 학습하면 좋은 개념

- **브랜치(branch)** — PR은 항상 브랜치 단위로 만들어진다. 브랜치를 먼저 이해해야 왜 PR이 필요한지 이어서 이해할 수 있다.
- **머지 충돌(merge conflict)** — PR을 merge할 때 자주 마주치는 상황. 미리 알아두면 실전에서 당황하지 않는다.
- **코드 리뷰(code review)** — PR의 핵심 목적 중 하나. 팀 작업에서 PR을 왜 쓰는지 이해하는 데 도움이 된다.

## 참고 자료

- [Git 공식 문서 - git-commit](https://git-scm.com/docs/git-commit)
- [Git 공식 문서 - git-push](https://git-scm.com/docs/git-push)
- [Git 공식 문서 - git-pull](https://git-scm.com/docs/git-pull)
- [GitHub Docs - About pull requests](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests)

<script src="https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.min.js"></script>
<script>mermaid.initialize({ startOnLoad: true });</script>
