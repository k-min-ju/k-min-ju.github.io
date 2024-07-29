---
title : "조건부 타입"
permalink: typescript/conditional-type
categories:
  - TypeScript
tags:
  - [TypeScript]
toc: true
toc_sticky: true
---

---
> extends와 제네릭을 활용한 조건부 타입

> extends 키워드는 타입을 확장할 때와 타입을 조건부로 설정할 때 사용되며, 제네릭 타입에서는 한정자 역할로도 사용된다. 

```typescript
T extends U ? X : Y
```

> 조건부 타입에서 extends를 사용할 때는 자바스크립트 삼항 연산자와 함께 쓴다. 이 표현은 타입 T를 U에 할당할 수 있으면 X 타입, 아니면 Y 타입으로 결정됨을 의미한다.

```typescript
interface Bank {
  code: string;
  name: string;
  fullName: string;
}

interface Card {
  code: string;
  name: string;
  type?: string;
}

type PayMethod<T> = T extends "card" ? Card : Bank;
type CardPayMethod = PayMethod<"card">;
type BandPayMethod = PayMethod<"bank">;
```

<br>

---

# infer를 활용해서 타입 추론하기
---
extends를 사용할 때 infer 키워드를 사용할 수 있다. infer는 '추론하다'라는 의미를 지니고 있는데 타입스크립트에서도 단어 의미처럼 타입을 추론하는 역할을 한다.
삼항 연산자를 사용한 조건문의 형태를 가지는데, extends로 조건을 서술하고 infer로 타입을 추론한다.
```typescript
type UnpackPromise<T> = T extends Promise<infer K>[] ? K : any;

const promises = [Promise.resolve("Test"), Promise.resolve(22)];
type Expected = UnpackPromise<typeof promises>; // string | number
```
