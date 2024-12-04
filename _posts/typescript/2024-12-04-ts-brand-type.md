---
title : "Brand Type Example"
permalink: typescript/brand-type
categories:
  - TypeScript
tags:
  - [TypeScript]
toc: true
toc_sticky: true
---

Brand 타입은 동일한 구조를 가진 타입을 구분하기 위해 사용된다.  
동일한 key값을 사용해야 할 때 Brand 타입을 사용하면 구조적으로 동일하지만 다른 의미를 갖는다.
```typescript
function testFunc(userId: string, productId: string): void {
  console.log(`userId:${userId} -- productId:${productId}`);
}

const userId: string = 'u';
const productId: string = 'p';
testFunc(userId, productId); // Success
testFunc(productId, userId); // Success
// 실제로 userId는 productId를 전달해도 동작한다. 이렇게 되면 의도하지 않은 값이 전달되어 오류를 발생시킬 수 있다.
// Brand 타입을 사용해서 각 key를 구분할 수 있다.

type Brand<K, T> = K & { __brand: T };
type UserId = Brand<string, 'UserId'>;
type ProductId = Brand<string, 'ProductId'>;

function brandTestFunc(userId: UserId, productId: ProductId): void {
  console.log(`userId:${userId} -- productId:${productId}`);
}

const b_userId: UserId = 'u' as UserId;
const b_productId: ProductId = 'p' as ProductId;
brandTestFunc(b_userId, b_productId); // Success
brandTestFunc(b_productId, b_userId); // Fail
```
<img width="581" alt="스크린샷 2024-12-04 오후 3 18 38" src="https://github.com/user-attachments/assets/c5bb1096-f22e-4703-8f87-d4608d26d00f">

<br>

---

<br>

특정 타입만 허용한다면 unique symbol을 사용하면 된다.
```typescript
type Brand<B> = { __brand: B };
type Branded<T, B> = T & Brand<B>;
const userIdSymbol: unique symbol = Symbol('UserId');
const productSymbol: unique symbol = Symbol('ProductId');
const productSymbol2: unique symbol = Symbol('ProductId');
type UserId = Branded<string, typeof userIdSymbol>;
type ProductId = Branded<string, typeof productSymbol>;
type ProductId2 = Branded<string, typeof productSymbol2>;

function brandTestFunc(userId: UserId, productId: ProductId): void {
  console.log(`userId:${userId} -- productId:${productId}`);
}

const userId: UserId = 'u' as UserId;
const productId: ProductId = 'p' as ProductId;
const productId2: ProductId2 = 'p' as ProductId2;
brandTestFunc(userId, productId); // Success
brandTestFunc(userId, productId2); // Fail
```
<img width="617" alt="스크린샷 2024-12-04 오후 5 06 07" src="https://github.com/user-attachments/assets/1903f975-f071-4a76-97a1-bae38a14d85c">

