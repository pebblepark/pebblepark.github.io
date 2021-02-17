---
title: Git Author 변경
category:
  - git
tags:
  - git
---

커밋을 잘못했을 때 `Author`를 바꾸는 법을 알아보자.

## git 계정정보 변경

```bash
$ git config user.name "해당아이디"
$ git config user.email "해당이메일"
```

- 로컬일 시에는 `--local` , 전역일 시에는 `--global`
- 만약 제대로 계정이 변경되지 않는다면 window 자격증명에서 git 정보 삭제하기

<br>

## git rebase로 커밋정보 변경하기
### 변경 커밋 선택

```bash
$ git rebase -i {변경 이전 커밋 ID}
```

- `git log` 명령어를 사용해서 변경하고 싶은 커밋의 바로 이전 커밋 id 넣기
- 변경하고 싶은 커밋 아이디를 사용하려면 커밋 아이디 뒤에 `^` 붙이기
  - `git rebase -i {변경 커밋 ID}^`

- 만약 `invalid upstream` 이 뜬다면 `git rebase -i --root` 사용하기
    - 자세한 부분은 참고의 stackoverflow 살펴보기

### 변경하기

- `vi`편집기로 들어가지면 변경하고 싶은 커밋을 `pick`에서 `edit`으로 변경하기
    - `vi`편집기의 수정모드는 `i` 누르기

```bash
$ git commit --amend --author="작성자명 <email주소>"

# 또는 git commit --amend 까지만 입력한 후 vi 에디터로 직접 수정하기
$ git commit --amend
```

### 변경 종료

```bash
$ git rebase --continue
Successfully rebased and updated refs/heads/master.
```

### 반영하기

```bash
$ git push origin +{브랜치명}

# 또는
$ git push origin -f {브랜치명}
```

## 사실...
- 가장 좋은 방법은 계정이 여러개라면 커밋 전에 `username` 과 `useremail` 확인하고 커밋해주자.

<br>

## 참고
- [https://stackoverflow.com/questions/33911379/git-rebase-fatal-needed-a-single-revision-invalid-upstream-i](https://stackoverflow.com/questions/33911379/git-rebase-fatal-needed-a-single-revision-invalid-upstream-i)

- [https://madplay.github.io/post/change-git-author-name](https://madplay.github.io/post/change-git-author-name)