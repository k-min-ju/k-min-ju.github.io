---
title : "타입 확장하기"
permalink: typescript/type-extention
categories:
  - TypeScript
tags:
  - [TypeScript]
toc: true
toc_sticky: true
---
```
💡 타입 확장은 기존 타입을 사용해서 새로운 타입을 정의하는 것을 말한다. 기본적으로 타입스크립트에서는 interface와 type 키워드를 사용해서 타입을 정의하고 extends,
교차 타입, 유니온 타입을 사용하여 타입을 확장한다.
```

---
> 타입 확장의 장점

타입 확장의 가장 큰 장점은 코드 중복을 줄일 수 있다는 것이다. 타입스크립트 코드를 작성하다 보면 필연적으로 중복되는 타입 선언이 생기기 마련이다. 
이때 중복되는 타입을 반복적으로 선언하는 것보다 기존에 작성한 타입을 바탕으로 타입 확장을 함으로써 불필요한 코드 중복을 줄일 수 있다.
```typescript
/**
 * 메뉴 요소 타입
 * 메뉴 이름, 이미지, 할인율, 재고 정보를 담고 있다.
 */ 
interface BaseMenuItem {
  itemName: string | null;
  itemImageUrl: string | null;
  itemDiscountAmount: number;
  stock: number | null;
};

/**
 * 장바구니 요소 타입
 * 메뉴 타입에 수량 정보가 추가되었다
 */
interface BaseCartItem extends BaseMenuItem {
  quantity: number;
};
```

<br>

이 예시에서 메뉴 타입을 기준으로 타입을 확장하여 장바구니 요소 타입을 장의한 것을 볼 수 있다. 다시 말해 장바구니 요소는 메뉴 요소가 가지는 모든 타입이 필요하다.
하지만 BaseMenuItem에 있는 속성을 중복해서 작성하지 않고 확장을 활용하여 타입을 정의했다. 이처럼 타입 확장은 중복된 코드를 줄일 수 있게 해준다.
그뿐만 아니라 BaseCartItem이 BaseMenuItem에서 확장되었다는 것을 쉽게 확인할 수 있는 것처럼 더 명시적인 코드를 작성할 수 있게 된다.
interface 키워드 대신 type을 쓴다면 이렇게 작성하면 된다.
```typescript
type BaseMenuItem = {
  itemName: string | null;
  itemImageUrl: string | null;
  itemDiscountAmount: number;
  stock: number | null;
};

type BaseCartItem = {
  quantity: number;
} & BaseMenuItem;
```

<br>

타입 확장은 중복 제거, 명시적인 코드 작성 외에도 확장성이란 장점을 가지고 있다. 앞에서 정의한 BaseCartItem을 활용하면 요구 사항이 늘어날 때마다 새로운
CartItem타입을 확장하여 정의할 수 있다.
```typescript
interface EditableCartItem extends BaseCartItem {
  isSoldOut: boolean;
  optionGroups: SelectableOptionGroup[];
};

interface EventCartItem extends BaseCartItem {
  orderable: boolean;
}
```
이 코드에서 BaseCartItem을 확장하여 만든 EditableCartItem, EventCartItem 타입을 볼 수 있다. 이렇게 타입 확장을 활용하면 장바구니와 관련된 요구 사항이
생길 때마다 필요한 타입을 손쉽게 만들 수 있다. 더욱이, 기존 장바구니 요소에 대한 요구 사항이 변경되어도 BaseCartItem 타입만 수정하면 되기 때문에 효율적이다.

---

<br>

> 유니온 타입

유니온 타입은 2개 이상의 타입을 조합하여 사용하는 방법이다. 집합 관점으로 보면 유니온 타입을 합집합으로 해석할 수 있다.
```typescript
type MyUnion = A | B;
```
A와 B의 유니온 타입인 MyUnion은 타입 A와 B의 합집합이다. 합집합으로 해석하면 집합 A의 모든 원소는 집합 MyUnion의 원소이며, 집합 B의 모든 원소 역시
집합 MyUnion의 원소라는 뜻이다. 즉 A 타입과 B 타입의 모든 값이 MyUnion 타입의 값이 된다.
여기서 주의해야 할 게 있다. 유니온 타입으로 선언된 값은 유니온 타입에 포함된 모든 타입이 공통으로 갖고 있는 속성에만 접근할 수 있다.

```typescript
interface CookingStep {
  orderId: string;
  price: number;
}

interface DeliveryStep {
  orderId: string;
  time: number;
  distance: string;
}

function getDeliveryDistance(step: CookingStep | DeliveryStep) {
  return step.distance;
  // property 'distance' does not exist on type 'CookingStep | DeliveryStep'
  // property 'distance' does not exist on type 'CookingStep'
}
```
getDeliveryDistance함수는 CookingStep과 DeliveryStep의 유니온 타입 값을 step이라는 인자로 받고 있다. 함수 본문에서는 step.distance를 호출하고 있는데
distance는 DeliveryStep에만 존재하는 속성이다. 인자로 받는 step의 타입이 CookingStep이라면 distance 속성을 찾을 수 없기 때문에 에러가 발생한다.
```
💡 타입 스크립트의 타입을 속성의 집합이 아니라 값의 집합이라고 생각해야 유니온 타입이 합집합이라는 개념을 이해할 수 있다.
```

<br>

---
> 교차 타입

교차 타입도 기존 타입을 합쳐 필요한 모든 기능을 가진 하나의 타입을 만드는 것으로 이해할 수 있다.
```typescript
interface CookingStep {
  orderId: string;
  time: number;
  price: number;
}

interface DeliveryStep {
  orderId: string;
  time: number;
  price: number;
}

type BaedalProgress = CookingStep & DeliveryStep;
```
여기서 유니온 타입과 다른 점이 있다. BaeDalProgress는 두 개의 타입을 합쳐 모든 속성을 가진 단일 타입이 된다.

```typescript
function logBaedalInfo(progress: BaedalProgress) {
  console.log(`주문 금액: ${progress.price}`);
  console.log(`배달 거리: ${progress.distance}`);
}
```
따라서 BaedalProgress타입의 progress값은 CookingStep이 갖고 있는 price속성과 DeliveryStep이 갖고있는 distance속성을 포함하고 있다.

---
교차 타입을 사용할 때 타입이 서로 호환되지 않는 경우도 있다.
```typescript
type IdType = string | number;
type Numeric = number | boolean;

type Universal = IdType & Numeric;
```
먼저 UniverSal타입을 다음과 같이 4가지로 생각해볼 수 있다.
1. string이면서 number인 경우
2. string이면서 boolean인 경우
3. number이면서 number인 경우
4. number이면서 boolean인 경우  

UniverSal은 IdType과 Numeric의 교차 타입이므로 두 타입을 모두 만족하는 경우에만 유지된다. 따라서 1, 2, 4번은 성립되지 않고 3번만 유효하기 때문에
UniverSal의 타입은 number가 된다.