---
title : "Pull Request"
permalink: git/github/pull/request
categories:
  - Git
tags:
  - [Git, GitHub]
toc: true
toc_sticky: true
---

```
프로젝트를 진행할 때 main, dev, dev-mj와 같이 branch가 존재한다. 
devbranch는 모든 개발자들이 개발을 진행한 후 commit을 하는 공간이므로 개발자마다 개인의 branch를 만들어 개발한다.
dev-mj에서 개발한 내용을 dev로 올리고 싶을 때 방법을 소개하려고 한다. 
gitHub의 pull request 방법에는 아래 이미지와 같이 Create a merge commit, Squash and merge, Rebase and merge 3가지가 존재한다.
```

<img src="../../../assets/images/git/pull-request.png">

1. Create a merge commit
> 이 방식을 사용할 경우 대상 branch의 가지가 뻗어 나오고 merge가 되었다는 commit 내용이 생성된다.
<img src="../../../assets/images/git/pull-request-commit.png">
2. Squash and merge
> devTest branch에서 commit을 10개 했다고 가정해 보자. dev에 merge 했을 때 10개의 commit 내역을 1개의 commit 내역으로 만들어 준다. commit 내역이 유실될 수 있으니 유의하자.
3. Rebase and merge
> 마찬가지로 commit을 10개 했다고 가정해 보자. Squash와는 다르게 merge를 했을 때 10개의 commit 내역이 그대로 dev에 생성된다.

<br>

개인적으로 1번 방식은 추천하지 않는다. 불필요한 commit 내역이 남는 게 싫달까..?  
쓸데없는 commit이 많아 하나로 합치고 싶을 때 Squash를 사용하고 commit 내역을 잘 관리했다면 Rebase로 모든 commit 내역을 merge 하면 될 것 같다.

