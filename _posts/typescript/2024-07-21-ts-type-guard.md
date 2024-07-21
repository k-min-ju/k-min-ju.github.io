---
title : "타입 가드"
permalink: typescript/type-guard
categories:
  - TypeScript
tags:
  - [TypeScript]
toc: true
toc_sticky: true
---

---
> 타입 좁히기 - 타입 가드

타입 좁히기란 변수 또는 표현식의 타입 범위를 더 작은 범위로 좁혀나가는 과정을 말한다. 타입 좁히기를 통해 더 정확하고 명시적인 타입 추론을 할 수 있게 되고,
복잡한 타입을 작은 범위로 축소하여 타입 안정성을 높일 수 있다.

<br>

---
> 원시 타입을 추론할 때: typeof 연산자 활용하기

typeof 연산자를 활용하면 원시 타입에 대해 추론할 수 있다. 자바스크립트의 동작 방식으로 인해 null과 배열 타입 등이 object 탕비으로 판별되는 등 복잡한 타입을
검증하기에는 한계가 있다. 따라서 typeof 연산자는 주로 원시 타입을 좁히는 용도로만 사용할 것을 권장한다.
```
아래는 typeof 연산자를 사용하여 검사할 수 있는 타입 목록이다.
- string
- number
- boolean
- undefined
- object
- function
- bigint
- symbol
```

<br>

---
> 인스턴스화된 객체 타입을 판별할 때: instanceof 연산자 활용하기

A instanceof B형태로 사용하며 A에는 타입을 검사할 대상 변수, B에는 특정 객체의 생성자가 들어간다. instanceof는 A의 프로토타입 체인에 생성자 B가 존재하는지를
검사해서 존재한다면 true. 그렇지 않다면 false를 반환한다. 
```typescript
const onKeyDown = (event: React.KeyboardEvent) => {
  if (event.target instanceof HTMLInputElement && event.key === 'Enter') {
    // 이 분기에서는 event.target의 타입이 HTMLInputElement이며
    // event.key가 'Enter'이다
    event.target.blur();
  }
}
```

<br>

---
> 객체의 속성이 있는지 없는지에 따른 구분: in 연산자 활용하기

in연산자는 객체에 속성이 있는지 확인한 다음에 true 또는 false를 반환한다. in연산자를 사용하면 속성이 있는지 없는지에 따라 객체 타입을 구분할 수 있다.
in연산자는 A in B의 형태로 사용하는데 이름 그대로 A라는 속성이 B객체에 존재하는지를 검사한다. 프로토타입 체인으로 접근할 수 있는 속성이면 전부 true를 반환한다.
in연산자는 B객체 내부에 A속성이 있는지 없는지를 검사하는 것이기 때문에 B객체에 존재하는 A속성에 undefined를 할당한다고 해서 false를 반환하는 것은 아니다.
delete연산자를 사용하여 객체 내부에서 해당 속성을 제거해야만 false를 반환한다.  
자바스크립트의 in연산자는 런타인의 값만을 검사하지만 타입스크립트에서는 객체 타입에 속성이 존재하는지를 검사한다.

<br>

---
> is연산자로 사용자 정의 타입 가드 만들어 활용하기

직접 타입 가드 함수를 만들 수도 있다. 타입 명제는 A is B형식으로 작성하면 되는데 여기서 A는 매개변수 이름이고 B는 타입이다. 참/거짓의 진릿값을 반환하면서
반환 타입을 타입 명제로 지정하게 되면 반환 값이 참일 때 A매개변수의 타입을 B타입으로 취급하게 된다. 

```typescript
const isDestinationCode = (x: string): x is DestinationCode => destinationCodeList.includea(x);
```
isDestinationCode는 string타입의 매개변수가 destinationCodeList배열의 원소 중 하나인지를 검사하여 boolean을 반환하는 함수이다.
함수의 반환 값을 boolean이 아닌 x is isDestinationCode로 타이핑하여 타입스크립트에게 이 함수가 사용되는 곳의 타입을 추론할 때 해당 조건을 타입 가드로
사용하도록 알려준다.