---
title : "타입 조합"
permalink: typescript/type-combination
categories:
  - TypeScript
tags:
  - [TypeScript]
toc: true
toc_sticky: true
---

---
# 1.교차 타입
```
교차 타입을 사용하면 여러 가지 타입을 결합하여 하나의 단일 타입으로 만들 수 있다.  
다시 말해 기존에 존재하는 다른 타입들을 합쳐서 해당 타입의 모든 멤버를 가지는 새로운 타입을 생성하는 것이다.  
교차 타입은 &을 사용해서 표기한다. 결과물로 탄생한 단일 타입에는 타입 별칭을 붙일 수도 있다.

예를 들어, 타입 C가 타입 A와 B의 교차 타입 즉, A & B라면 타입 C는 타입 A와 타입 B의 모든 멤버를 가지고 있는 타입이다.  
당연히 2개의 타입뿐만 아니라 여러 개의 타입을 교차시킬 수도 있다.

아래처럼 ProductItemWithDiscount 타입의 변수를 선언하고 값을 할당하면 ProductItem의 모든 멤버와 discountAmount까지 멤버로 가지게 된다.
```
```typescript
type ProductItem = {
  id: number;
  name: string;
  type: string;
  price: number;
  imageUrl: string;
  quantity: number;
}

type ProductItemWithDiscount = ProductItem & { discountAmount: number; };
```

<br>

# 2.유니온 타입(Union)
---
```
교차 타입(A & B)이 타입 A와 타입 B를 모두 만족하는 경우라면, 유니온 타입은 타입 A 또는 타입 B 중 하나가 될 수 있는 타입을 말하며 A | B같이 표기한다.  
주로 특정 변수가 가질 수 있는 타입을 전부 나열하는 용도로 사용된다. 교차 타입과 마찬가지로 2개 이상의 타입을 이어 붙일 수 있고 타입 별칭을 통해 중복을 줄일 수도 있다.  
아래 예시는 ProductItem 혹은 CardItem이 될 수 있는 유니온 타입인 PrompotionEventItem을 나타낸다. 즉, 이벤트 프로모션의 대상으로 상품이 될 수도 있고 카드가 될 수도 있다는 의미이다.  

아래 PrintPromitionItem() 함수를 보면 인자로 PromitionEventItem 타입을 받고 있다. 해당 함수 내부에서 quantity를 참조하려고 시도하면 컴파일 에러가 발생하는데,  
이는 quantity가 ProductItem에만 존재하기 때문이다. PromotionEventItem은 CardItem도 포함하는데 CardItem은 quantity 멤버를 가지고 있지 않기 때문에 PromotionEventItem에서는 quantity를 참조할 수 없다.
```
```typescript
type CardItem = {
  id: number;
  name: string;
  type: string;
  imageUrl: string;
};

type PromotionEventItem = ProductItem | CardItem;

const printPromotionitem = (item: PromotionEventItem) => {
  console.log(item.name);
  
  console.log(item.quantity); // 컴파일 에러 발생
}
```
참고로 교차 타입과 유니온 타입은 여러 줄에 걸쳐 표기할 수도 있는데, 이럴 경우에는 각 줄의 맨 앞에 & 혹은 |를 붙여서 표기하면 된다.
```typescript
type PromotionEventItem = 
| ProductItem
| CardItem;
```

<br>

# 3.인덱스 시그니처(Index Signatures)
```
인덱스 시그니처는 특정 타입의 속성 이름은 알 수 없지만 속성값의 타입을 알고 있을 때 사용하는 문법이다.  
인터페이스 내부의 [Key: K]: T꼴로 타입을 명시해주면 되는데 이는 해당 타입의 속성 키는 모두 K 타입이어야 하고 속성값은 모두 T 타입을 가져야 한다는 의미다.  
```
```typescript
interface IndexSignatureEx {
  [key: string]: number;
}
```
```
인덱스 시그니처를 선언할 때 다른 속성을 추가로 명시해줄 수 있는데 이때 추가로 명시된 속성은 인덱스 시그니처에 포함되는 타입이어야 한다. 아래 예시의 name은  
string 타입을 가지도록 선언되어 있지만, 인덱스 시그니처의 키가 string일 때는 number | boolean 타입이 오게끔 선언되어 있기 때문에 에러가 발생한다.
```
```typescript
interface IndexSignatureEx2 {
  [key: string]: number | boolean;
  length: number;
  isValid: boolean;
  name: string; // 에러 발생
}
```

<br>

# 4.인덱스드 엑세스 타입(Indexed Access Types)
---
```
인덱스드 엑세스 타입은 다른 타입의 특정 속성이 가지는 타입을 조회하기 위해 사용된다.  
아래 첫 번째 예시(IndexedAccess)는 Example 타입의 a 속성이 가지는 타입을 조회하기 위한 인덱스드 엑세스 타입이다.  
인덱스에 사용되는 타입 또한 그 자체로 타입이기 때문에 유니온 타입. keyof, 타입 별칭 등의 표현을 사용할 수 있다.
```
```typescript
type Example = {
  a: number;
  b: string;
  c: boolean;
}

type IndexAccess = Exmaple["a"];
type IndexAccess2 = Exmaple["a" | "b"]; // number | string
type IndexAccess = Exmaple[keyof Exmaple]; // number | string | boolean

type ExAlias = "b" | "c";
type IndexedAccess4 = Example[ExAlias]; // string | boolean
```
```
또한 배열의 요소 타입을 조회하기 위해 인덱스드 엑세스 ㄴ타입을 사용하는 경우가 있다.  
배열 타입의 모든 요소는 전부 동일한 타입을 가지며 배열의 인덱스는 숫자 타입이다.  
따라서 number로 인덱싱하여 배열 요소를 얻은 다음에 typeof 연산자를 붙여주면 해당 배열 요소의 타입을 가져올 수 있다.
```
```typescript
const PromotionList = [
  { type: "product", name: "chicken" },
  { type: "product", name: "pizza" },
  { type: "card", name: "cheer-up" }
];

type ElementOf<T> = typeof T[number];

// type PromotionItemType = { type: string; name: string };
type PromotionItemType = ElementOf<PromotionList>;
```

<br>

# 5.맵드 타입(Mapped Types)
---
```
보통 map이라고 하면 유사한 형태를 가진 여러 항목의 목록 A를 변환된 항목의 목록 B로 바꾸는 것을 의미한다.  
자바스크립트의 map 메서드를 생각하면 이해하기 쉬울 것이다. 자바스크립트의 map은 배열 A를 기반으로 새로운 배열 B를 만들어내는 배열 메서드이다.  
이와 마찬가지로 맵드 타입은 다른 타입을 기반으로 한 타입을 선언할 때 사용하는 문법인데, 인덱스 시그니처 문법을 사용해서 반복적인 타입 선언을 효과적으로 줄일 수 있다.
```
```typescript
type Exmaple = {
  a: number;
  b: string;
  c: boolean;
};

type Subset<T> = {
  [K in keyof T]?: T[K];
};

const aExmaple: Subset<Exmaple> = { a: 3 };
const bExmaple: Subset<Exmaple> = { b: "Hello" };
const acExmaple: Subset<Exmaple> = { a: 4, c: true };
```
```
맵드 타입에서 매핑할 때는 readonly와 ?를 수식어로 적용할 수 있다. 이미 알고 있겠지만 readonly는 읽기 전용으로 만들고 싶을 때 붙여주는 수식어이고,  
?는 선택적 매개변수(옵셔널 파라미터)로 만들고 싶을 때 붙여주는 수식어이다. 맵드 타입의 특이한 점은 이러한 수식어를 더해주는 것뿐만 아니라 제거할 수도 있다는 것이다.  
기존 타입에 존재하던 readonly나 ? 앞에 - 를 붙여주면 해당 수식어를 제거한 타입을 선언할 수 있다.
```
```typescript
type ReadOnlyEx = {
  readonly a: number;
  readonly b: string;
};

type CreateMutable<Type> = {
  -readonly [Property in keyof Type]: Type[Property];
};

type ResultType = CreateMutable<ReadOnlyEx>l // { a: number; b: string; }

type OptionalEx = {
  a?: number;
  b?: string;
  c: boolean;
};

type Concrete<Type> = {
  [Property in keyof Type]-?: Type[Property];
};

type ResultType = Concrete<OptionalEx>; // { a: number; b: string; c: boolean; }
```
```
맵드 타입이 실제로 사용된 예시를 살펴보자. 배달의민족 선물하기 서비스에는 '바텀시트'라는 컴포넌트가 존재한다. 밑에서부터 스르륵 올라오는 모달이라고 생각하면 되는데  
이 바텀시트는 선물하기 서비스의 최근 연락처 목록, 카드 선택, 상품 선택 등 여러 지면에서 사용되고 있다. 바텀시트마다 각각 resolver, isOpened 등의 상태를 관리하는  
스토어가 필요한데 이 스토어의 타입 (BottomSheetStore)을 선언해줘야 한다.

이 때 BottomSheetMap에 존잴하는 모든 키에 대해 일일이 스토어를 만들어줄 수도 있지만 불 필요한 반복이 발생하게 된다. 이럴 때는 인덱스 시그니처 문법을 사용해서  
BottomSheetMap을 기반으로 각 키에 해당하는 스토어를 선언할 수 있다. 이처럼 반복 작업을 효율적으로 처리할 수 있다.
```
```typescript
const BottomSheetMap = {
  RECENT_CONTACTS: RecentContactsBottomSheet,
  CARD_SELECT: CardSelectBottomSheet,
  SORT_FILTER: SortFilterBottomSheet,
  PRODUCT_SELECT: ProductSelectBottomSheet,
  REPLY_CARD_SELECT: ReplyCardSelectBottomSheet,
  RESEND: ResendBottomSheet,
  STICKER: StickerBottomSheet,
  BASE: null
};

export type BOTTOM_SHEET_ID = keyof typeof BottomSheetMap;

// 불필요한 반복이 발생한다.
type BottomSheetStore = {
  RECENT_CONTACTS: {
    resolver?: (payload: any) => void;
    args?: any;
    isOpened: boolean;
  };
  CARD_SELECT: {
    resolver?: (payload: any) => void;
    args?: any;
    isOpened: boolean;
  };
  SORT_FILTER: {
    resolver?: (payload: any) => void;
    args?: any;
    isOpened: boolean;
  };
  // ...
};

// Mapped Types를 통해 효율적으로 타입을 선언할 수 있다.
type BottomSheetStore = {
  [index in BOTTOM_SHEET_ID]: {
    resolver?: (payload: any) => void;
    args?: any;
    isOpened: boolean;
  };
};
```
```
덧붙여 맵드 타입에서는 as 키워드를 사용하여 키를 재지정할 수 있다. 앞서 봤던 바텀시트를 다시 살펴보자. BottomSheetStore의 키 이름에 BottomSheetMap의 키 이름을  
그대로 쓰고싶은 경우가 있을 수 있고, 모든 키에 _BOTTOM_SHEET를 붙이는 식으로 공통된 처리를 적용하여 새로운 키를 지정하고 싶을 수도 있다. 이럴 때는 아래 예시처럼  
as 키워드를 사용해서 효율적으로 처리할 수 있다.
```
```typescript
type BottomSheetStore = {
  [index in BOTTOM_SHEET_ID as `${index}_BOTTOM_SHEET`]: {
    resolver?: (payload: any) => void;
    args?: any;
    isOpened: boolean;
  }
};
```

<br>

# 6.템플릿 리터럴 타입(Template Literal Types)
---
```
템플릿 리터럴 타입은 자바스크립트의 템플릿 리터럴 문자열을 사용하여 문자열 리터럴 타입을 선언할 수 있는 문법이다.  
위에서 본 BottomSheetMap의 각 키에다 _BOTTOM_SHEET를 붙여주는 예시가 바로 템플릿 리터럴 타입을 활용한 것이다.  
조금 더 간단한 활용 예시를 살펴보자.
```
```typescript
type Stage =
  | "init"
  | "select-image"
  | "edit-image"
  | "decorate-card"
  | "capture-image"
type StageName = `${Stage}-stage`;
// 'init-stage' | 'select-image-stage' | 'edit-image-stage' | 'decorate-image-stage' | 'capture-image-stage'
```
```
Stage 타입의 모든 유니온 멤버 뒤에 -stage를 붙여서 새로운 유니온 타입을 만들었다. 여기서 볼 수 있듯이 템플릿 리터럴을 사용하여 `${Stage}-stage`와 같이  
변수 자리에 문자열 리터럴의 유니온 타입인 Stage를 넣으면 해당 유니온 타입(Stage 타입) 멤버들이 차례대로 해당 변수에 들어가서 -stage가 붙은 문자열 리터럴의  
유니온 타입을 결과로 반환한다. 즉, Stage 타입의 각 멤버에 -stage를 추가하여 init-stage, select-image-stage, edit-image-stage, decorate-card-stage,  
capture-image-stage와 같은 새로운 문자열 리터럴 유니온 타입을 만들었다.
```

<br>

# 7.제네릭(Generic)
---
```
제네릭은 C나 자바 같은 정적 언어에서 다양한 타입 간에 재사용성을 높이기 위해 사용하는 문법이다. 타입스크립트도 정적 타입을 가지는 언어이기 때문에 제네릭 문법을 지원하고 있다.  
제네릭의 사전적 의미를 찾아보면 특징이 없거나 일반적인 것(not specific, general)을 뜻한다. 타입스크립트의 제네릭도 이와 비슷한 맥락을 가지고 있는데 한마디로  
일반화된 데이터 타입이라고 할 수 있다. 좀 더 자세히 타입스크립트 제네릭의 개념을 풀어보면 함수, 타입, 클래스 등에서 내부적으로 사용할 타입을 미리 정해두지 않고  
타입 변수를 사용해서 해당 위치를 비워 둔 다음에, 실제로 그 값을 사용할 때 외부에서 타입 변수 자리에 타입을 지정하여 사용하는 방식을 말한다.  
이렇게 하면 함수, 타입, 클래스 등 여러 타입에 대해 하나하나 따로 정의하지 않아도 되기 때문에 재사용성이 크게 향상된다. 타입 변수는 일반적으로 <T>와 같이  
꺾쇠괄호 내부에 정의되며, 사용할 때는 함수에 매개변수를 넣는 것과 유사하게 원하는 타입을 넣어주면 된다. 보통 타입 변수명으로 T(Type0, E(Element), K(key), V(Value) 등  
한 글자로 된 이름을 많이 사용한다.
```
```typescript
type ExampleArrayType<T> = T[];

const array1: ExampleArrayType<string> = ["치킨", "피자", "우동"];
```
```
앞서 제네릭이 일반화된 데이터 타입을 말한다고 했는데, 이 표현만 보면 any의 쓰임과 혼동할 수도 있을 것이다. 하지만 둘은 명확히 다르다. 둘의 차이는 배열을 떠올리면 쉽게 알 수 있다.  
any타입의 배열에서는 배열 요소들이 타입이 전부 같지 않을 수 있다. 쉽게 말해 타입 정보를 잃어버린다고 생각하면 편하다. 즉, any를 사용하면 타입 검사를 하지 않고 모든 타입이 허용되는 타입으로 취급된다.  
반면에 제네릭은 any처럼 아무 타입이나 무분별하게 받는 게 아니라, 배열 생성 시점에 원하는 타입으로 특정할 수 있다. 다시 말해 제네릭을 사용하면 배열 요소가 전부 동일한 타입이라고 보장할 수 있다.
```
```typescript
type ExampleArrayType2 = any[];

const array2: ExampleArrayType2 = [
  "치킨",
  {
    id: 0,
    name: "치킨",
    price: 20000,
    quantity: 1
  },
  99,
  true
];
```
```
참고로 제네릭 함수를 호출할 때 반드시 꺾쇠괄호(<>)안에 타입을 명시해야 하는 것은 아니다. 타입을 명시하는 부분을 생략하면 컴파일러가 인수를 보고 타입을 추론해준다.  
따라서 타입 추론이 가능한 경우에는 타입 명시를 생략할 수 있다.
```
```typescript
function exampleFunc<T>(arg: T): T[] {
  return new Array(3).fill(arg);
}

exampleFunc("hello"); //T는 string으로 추론된다
```
```
또한 특정 요소 타입을 알 수 없을 때는 제네릭 타입에 기본값을 추가할 수 있다.
```
```typescript
interface SubmitEvent<T = HTMLElement> extends SyntheticEvent<T> { submitter: T; }
```
```
다시 언급하지만 제네릭은 일반화된 데이터 타입을 의마한다고 했다. 따라서 함수나 클래스 등의 내부에서 제네릭을 사용할 때 어떤 타입이든 될 수 있다는 개념은 알고 있어야 한다.  
특정한 타입에서만 존재하는 멤버를 참조하려고 하면 당연히 에러가 발생한다. 컴파일러는 어떤 타입이 제네릭에 전달될지 알 수 없기 때문에 모든 타입이 length 속성을 사용할 수는 없다고 알려주는 것이다.
```
```typescript
function exmapleFunc2<T>(arg: T): number {
  return arg.length; // 에러 발생: Property 'length' does not exist on type 'T'
}
```
```
이럴 때는 제네릭 꺾쇠괄호 내부에 'length 속성을 가진 타입만 받는다'라는 제약을 걸어줌으로써 length 속성을 사용할 수 있게끔 만들 수 있다.
```
```typescript
interface TypeWidthLength {
  length: number;
}

function exampleFunc2<T extends TypeWithLength>(arg: T): number {
  return arg.length;
}
```
```
제네릭을 사용할 때 주의해야 할 점이 있다. 파일 확장자가 tsx일 때 화살표 함수에 제네릭을 사용하면 에러가 발생한다. tsx는 타입스크립트 + JSX이므로  
제네릭의 꺾쇠괄호와 태그의 꺾쇠괄호를 혼동하여 문제가 생기는 것이다. 
```

<span style="color: skyblue">💡 JSX는 HTML과 유사한 구문을 사용하여 컴포넌트 를 작성하는 데 사용되는 기술이다. JSX에서는 태그를 나타내는 데 꺾쇠괄호(<>)를 사용한다.</span>

```
이러한 상황을 피하기 위해서는 제네릭 부분에 extends 키워드를 사용하여 컴파일러에게 특정 타입의 하위 타입만 올 수 있음을 확실히 알려주면 된다.  
보통 제네릭을 사용할 때는 function 키워드로 선언하는 경우가 많다.
```
```typescript
// 에러 발생: JSX element 'T' has no corresponding closing tag
const arrogwExmapleFunc = <T>(arg: T): T[] => {
  return new Array(3).fill(arg);
}

// 에러 발생 X
const arrowExampleFunc2 = <T extends {}>(arg: T): T[] => {
  return new Array(3).fill(arg);
}
```
