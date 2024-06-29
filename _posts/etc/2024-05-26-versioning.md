---
title : "Versioning"
permalink: etc/versioning
categories:
  - Etc
tags:
  - Etc
toc: true
toc_sticky: true
---

---
`<Major>.<Minor>.<Patch>[-<Tag><HotFix>]`

```
1. Major (주 번호)  
  1-1 기존 버전과 호환되지 않는 기능 추가 또는 삭제 시 버전 업 
  1-2 이하 버전 초기화
```

```
2. Minor (부 번호)
  2-1 기존 버전과 호환되는 새로운 기능 추가 또는 일부 삭제 시 버전 업
  2-2 이하 버전 초기화
```

```
3. Patch (수정 번호)
  3-1 기존 버전과 호환되는 버그 수정, 기능 추가 시 버전 업
  3-2 기능이 대폭 추가 또는 개선되거나 예정된 제거 기능 표시가 포함되는 경우 부 버전으로 올릴 수 있다. 유동적으로 상황에 따라 반영한다.
```

```
4. Tag
  4-1. alpha (알파 버전)
    4-1-1. 내부 개발 테스트 버전
    4-1-2. 로컬 서버에서 테스트
  4-2. beta (베타 버전)
    4-2-1. 개발 최종 완성 전에 검사를 하기 위한 버전
    4-2-2. 개발 서버에서 테스트
  4-3. ~~release candidate (발매 버전 후보)~~
    4-3-1. ~~베타 버전 보다 한 단계 개선된 버전~~
    4-3-2. ~~운영 서버에 배포하여~~ `rc1` ~~>~~ `rc2` ~~>~~ `rc3`~~식의 과정을 거친 후 최종 배포를 진행~~
    4-3-3. 우리는 해당 Tag는 사용하지 않는다.
  4-4. 발매 버전
    4-4-1. 최종 배포 버전
    4-4-2. Tag는 생략하며, 필요에 따라 **stable** 표기를 사용한다.
    4-4-3. **stable** 표기 사용 시 **stable version branch**를 생성한다.
5. Hot Fix
  5-1. 수정 카운트를 Tag 뒤에 증가시킨다.
```
# **형상 관리 요구사항**

---

각 Tag 별로 branch 관리가 되어야한다. 작업용/배포용 코드로 나누어 project 진행하기 위해 꼭 필요하다.

- 작업용 branch : alpha, beta
- 배포용 branch : main

# **버전 표기 방법**

---

최초 개발을 진행할 때는 `x.y.z`로 시작한다. 초기 개발을 위해 사용하는 `x.y.z`는 아무때나 마음대로 변경이 가능하다. Software가 운영 서비스에 쓰이기 시작하면 `1.0.0`으로 시작한다.

## **프로젝트 시작**

| PHASE | START | Iteration 1 | Iteration 2 | Iteration 3 |
| --- | --- | --- | --- | --- |
| 1 | v0.1.0-alpha | v0.1.1-alpha | v0.1.2-alpha |  |
| 2 | v1.0.0-alpha | v1.0.1-alpha |  |  |
| 3 | v1.1.0-alpha | v1.1.1-alpha | v1.1.2-alpha | v1.1.3-alpha |
| 4 | v.2.0.0-alpha | v2.0.1-alpha | v2.0.2-alpha |  |

## **베타 테스트 시작**

| PHASE | LATEST ALPHA | Bug Fix 1 | Bug Fix 2 | Bug Fix 3 |
| --- | --- | --- | --- | --- |
| 1 | v0.1.2-beta | v0.1.2-beta1 | v0.1.2-beta2 | v0.1.2-beta3 |
| 2 | v1.0.1-beta | v1.0.1-beta1 | v1.0.1-beta2 |  |
| 3 | v1.1.3-beta | v1.1.3-beta1 | v1.1.3-beta2 |  |
| 4 | v2.0.2-beta | v2.0.2-beta1 | v2.0.2-beta2 | v2.0.2-beta3 |

## **최종 버전 시나리오**

| LATEST ALPHA | LATEST BETA | FINAL RELEASE |
| --- | --- | --- |
| v0.1.2-alpha | v0.1.2-beta3 | v1.0.0 |
| v1.0.1-alpha | v1.0.1-beta2 | v1.1.0 |
| v1.1.3-alpha | v1.1.3.beta2 | v1.2.0 or v2.0.0 |
| v2.0.2-alpha | v2.0.2-beta3 | v2.1.0 |