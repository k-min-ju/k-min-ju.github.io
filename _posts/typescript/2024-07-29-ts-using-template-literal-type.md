---
title : "템플릿 리터럴 타입 활용하기"
permalink: typescript/using-template-literal-type
categories:
  - TypeScript
tags:
  - [TypeScript]
toc: true
toc_sticky: true
---

타입스크립트에서는 유니온 타입을 사용하여 변수 타입을 특정 문자열로 지정할 수 있다.
```typescript
type HeaderTag = "h1" | "h2" | "h3" | "h4" | "h5";
```

<br>

이 기능을 사용하면 컴파일타임의 변수에 할당되는 타입을 특정 문자열로 정확하게 검사하여 휴먼 에러를 방지할 수 있고, 자동 완성 기능을 통해 개발 생산성을 높일 수 있다.  
TS4.1부터 템플릿 리터럴 타입을 지원하기 시작했다. 자바스크립트의 템플릿 리터럴 문법을 사용해 특정 문자열에 대한 타입을 선언할 수 있는 기능이다.  
```typescript
type HeadingNumber = 1 | 2 | 3 | 4 | 5;
type HeaderTag = `h${HeadingNumber}`;
```

아래의 Direction 타입에 템플릿 리터럴 타입을 적용하면 더 명확하게 표시할 수 있다.
```typescript
type Direction = "top" | "topLeft" | "topRight" | "bottom" | "bottomLeft" | "bottomRight";

type Vertical = "top" | "bottom";
type Horizon = "left" | "right";
```

<br>
> 주의할 점  

타입스크립트 컴파일러가 유니온을 추론하는 데 시간이 오래 골리면 비효율적이기 때문에 타입스크립트가 타입을 추론하지 않고 에러를 내뱉을 때가 있다.
따라서 템플릿 리터럴 타입에 삽입된 유니온 조합의 경우의 수가 너무 많지 않게 적절하게 나누어 타입을 정의하는 것이 좋다.
PhoneNumberType은 10000제곱 개의 경우의 수를 가지는 유니온 타입이 되기 때문에 에러가 발생할 수도 있다. 

```typescript
type Digit = 0 | 1 | 2 | 4 | 5 | 6 | 7 | 8 | 9;
type Chunk = `${Digit}${Digit}${Digit}${Digit}`;
type PhoneNumber= `010-${Chunk}-${Chunk}`;
```