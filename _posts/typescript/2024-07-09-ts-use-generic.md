---
title : "제네릭 사용법"
permalink: typescript/use-generic
categories:
  - TypeScript
tags:
  - [TypeScript]
toc: true
toc_sticky: true
---

---
> 함수의 제네릭

어떤 함수의 매개변수나 반환 값에 다양한 타입을 넣고 싶을 때 제네릭을 사용할 수 있다. 아래 예시처럼 T 자리에 넣는 타입에 따라 ReadOnlyRepository가 적절하게 사용될 수 있다.
```typescript
function ReadOnlyRepository<T>(target: ObjectType<T> | EntitySchema<T> | string): Repository<T> {
  return getConnection("ro").getRepository(target);
}
```

<br>
---
> 호출 시그니처의 제네릭

호출 시그니처는 타입스크립트의 함수 타입 문법으로 함수의 매개변수와 반환 타입을 미리 선언하는 것을 말한다. 호출 시그니처를 사용함으로써 개발자는 함수 호출시 필요한 타입을 
별도로 지정할 수 있게 된다. 호출 시그니처를 사용할 때 제네릭 타입을 어디에 위치시키는지에 따라 타입의 범위와 제네릭 타입을 언제 구체 타입으로 한정할지를 결정할 수 있다.
```typescript
interface useSelectPaginationProps<T> {
  categoryAtom: RecoilState<number>;
  filterAtom: RecoilState<string[]>; 
  sortAtom: RecoilState<SortType>;
  fetcherFunc: (props: CommonListRequest) => Promise<DefaultResponse<ContentListResponse<T>>>;
}
```

<br>
이 코드는 호출 시그니처 제네릭 활용 예시다. 여기서 &lt;T&gt;는 useSelectPaginationProps의 타입 별칭으로 한정했다. 따라서 useSelectPaginationProps을 사용할 때 
타입을 명시함으로써 제네릭 타입을 구체 타입으로 한정한다. 설명을 덧붙이면 useSelectPaginationProps가 사용되는 useSelectPagination훅의 반환 값도 인자에서 쓰는 
제네릭 타입인 T와 연관 있기 때문에 이처럼 작성했다.

```typescript
export type UseRequesterHookType = <RequestData = void, ResponseData = void>(
  baseURL?: string | Headers,
  defaultHeader?: Headers
) => [RequestStatus, Requester<RequestData, ResponseData>];
```

<br>
이 예시에서 <RequestData, ResponseData>는 호출 시그니처의 일부, 다시 말해 괄호()앞에 선언했기 때문에 타입스크립트는 UseRequesterHookType 타입의 함수를 
실제 호출할 때 제네릭 타입을 구체 타입으로 한정한다.

```tsx
// 배민 예시

function useSelectPagination<T extends CardListContent | CommonProductResponse>({
  categoryAtom,
  filterAtom,
  sortAtom,
  fetcherFunc
}: useSelectPaginationProps<T>): {
  intersectionRef: RefObject<HTMLDivElement>;
  data: T[];
  categoryId: number;
  isLoading: boolean;
  isEmpty: boolean;
} {
  // ...
  
  return {
    intersectionRef,
    data: swappedData ?? [],
    isLoading,
    categoryId,
    isEmpty
  };
}
```

<br>
---
> 제네릭 클래스

제네릭 클래스는 외부에서 입력된 타입을 클래스 내부에 적용할 수 있는 클래스이다. 제네릭 클래스는 다음과 같은 형태로 선언된다.
```ts
class LocalDB<T> {
  // ...
  async put(table: string, row: T): Promise<T> {
    return new Promise<T>((resolved, rejected) => { /* T 타입의 데이터를 DB에 저장 */});
  }
  
  async get(table: string, key: any): Promise<T> {
    return new Promise<T>((resolved, rejected) => { /* T 타입의 데이터를 DB에서 가져옴 */ });
  }
  
  async getTable(table: string): Promise<T[]> {
    return new Promise<T[]>((resolved, rejected) => { /* T[] 타입의 데이터를 DB에서 가져옴 */ })
  }
  
  export default class IndexedDB implements ICacheStore {
    private _DB?: LocalDB<{ key: string; value: Promise<Record<string, unknown>>; cacheTTL: number; }>;
    
    private DB() {
      if (!this._DB) {
        this._DB = new LocalDB("localCache", { ver: 6, tables: [{ name: TABLE_NAME, keyPath: "key" }] });
      }
      return this._DB;
    }
    // ...
  }
}
```

클래스 이름 뒤에 타입 매개변수인 &lt;T&gt;를 선언해준다. &lt;T&gt;는 메서드의 매개변수나 반환 타입으로 사용될 수 있다. LocalDB 클래스는 외부에서 { key: string; 
value: Promise<Record<string, unknown&gt;&gt; cacheTTL: number } 타입을 받아들여 클래스 내부에서 사용될 제네릭 타입으로 결정된다.
제네릭 클래스를 사용하면 클래스 전체에 걸쳐 타입 매개변수가 적용된다. 특정 메서드만을 대상으로 제네릭을 적용하려면 해당 메서드를 제네릭 메서드로 선언하면 된다.

<br>
---
> 제한된 제네릭

타입스크립트에서 제한된 제네릭은 타입 매개변수에 대한 제약 조건을 설정하는 기능을 말한다. 타입 매개변수 T 타입을 제약하는 방법을 알아보자.
예를 들어 string 타입으로 제약하려면 타입 매개변수는 특정 타입을 상속(extends)해야한다.
```typescript
type ErrorRecord<Key extends string> = Exclude<Key, ErrorCodeType> extends never ? Partial<Record<Key, boolean>> : never;
```
이처럼 타입 매개변수가 특정 타입으로 묶였을 때(bind) 키를 바운드 타이브 매개변수라고 부른다. 그리고 string을 키의 상한 한계라고 한다.
상속받을 수 있는 타입으로는 기본 타입뿐만 아니라 상황에 따라 인터페이스나 클래스도 사용할 수 있다. 또한 유니온 타입을 상속해서 선언할 수도 있다.
```typescript
function useSelectPagination<T extends CardListContent | CommonProductResponse>({
  filterAtom,
  sortAtom,
  fetcherFunc,
}: useSelectPaginationProps<T>): {
  intersectionRef: RefObject<HTMLDivElement>;
  data: T[];
  categoryId: number;
  isLoading: boolean;
  isEmpty: boolean;
} {
  // ...
}

// 사용하는 쪽 코드
const { intersectionRef, data, isLoading, isempty } = useSelectPagination<CardListContent>({
  categoryAtom: replyCardCategoryIdAtom,
  filterAtom; replyCardFilterAtom,
  sortAtom: replyCardSortAtom,
  fetcherFunc: fetchReplyCardLitBtyThemeGroup
)};
```

<br>
---
> 확장된 제네릭

제네릭 타입은 여러 타입을 상속받을 수 있으며 타입 매개변수를 여러 개 둘 수도 있다.
```typescript
<Key extends string>
```
타입을 이런식으로 제약해버리면 제네릭의 유연성을 잃어버린다. 제네릭의 유연성을 잃지 않으면서 타입을 제약해야 할 때는 타입 매개변수에 유니온 타입을 상속해서 선언하면 된다.

```typescript
<Key extends string | number>
```
유니온 타입으로 T가 여러 타입을 받게 할 수는 있지만, 타입 매개변수가 여러 개일 때는 처리할 수 없다. 이럴 때는 매개변수를 하나 더 추가하여 선언한다.
다음은 Ok 타입이나 Err 타입을 매개변수 인자로 받아 사용하는 예시이다.

```typescript
export class APIResponse<Ok, Err = string> {
  private readonly data: Ok | Err | null;
  private readonly status: ResponseStatus;
  private readonly statusCode: number | null;

  constructor (
    data: Ok | Err | null,
    statusCode: number | null,
    status: ResponseStatus
  ) {
    this.data = data;
    this.status = status;
    this.statusCode = statusCode;
  }
  
  public static Success<T, E = string>(data: T): APIResponse<T, E> {
    return new this<T, E>(data, 200, ResponseStatus.SUCCESS);
  }
  
  public static Error<T, E = unknown>(init: AxiosError): APIResponse<T, E> {
    if (!init.response) {
      return new this<T, E>(null, null, ResponseStatus.CLIENT_ERROR);
    }
    
    if (!init.response.data?,result) {
      return new this<T, E>(
        null,
        init.response.status,
        ResponseStatus.SERVER_ERROR
      );
    }
    
    return new this<T, E>(
      init.response.data.result,
      init.response.status,
      ResponseStatus.FAILURE
    );
  }
  
  // ...
}

// 사용하는 쪽 코드
const fetchShopStatus = async (): Promise<ApiResponse<IShopResponse | null>> => {
  // ...
  
  return (await API.get<IShopResponse | null>("/v1/main/shop", config)).map(
    (it) => it.result
  );
};
```

<br>

> 제네릭 예시

제네릭의 장점은 다양한 타입을 받게 함으로써 코드를 효율적으로 재사용할 수 있는 것이다. 그렇다면 실제 현업에서 가장 많이 제네릭이 활용할 때는 언제일까? 바로 API 응답 값의 타입을 지정할 때이다.
API 응답 값의 타입을 지정할 때 제네릭을 활용하여 적절한 타입 추론과 코드의 재사용성을 높이고 있다.
```typescript
export interface MobileApiResponse<Data> {
  data: Data;
  statusCode: string;
  statusMessage?: string;
}
```
이 코드를 살펴보면 API 응답 값에 따라 달라지는 data를 제네릭 타입 Data로 선언하고 있다. 이렇게 만든 MobileApiResponse는 실제 API 응답 값의 타입을 지정할 때 아래와 같이 사용되고 있다.
```typescript
export const fetchPriceInfo = (): Promise<MobileApiResponse<PriceInfo>> => {
  const priceUrl = "https:~~~"; // url 주소
  
  return request({
    method: "GET",
    url: orderUrl
  });
};
```
이처럼 다양한 API 응답 값의 타입에 MobileApiResponse을 활용해서 코드를 효율적으로 재사용할 수 있다.
이런 식으로 제네릭을 필요한 곳에 사용하면 가독성을 높이고 코드를 효율적으로 작성할 수 있다. 하지만 굳이 필요하지 않은 곳에 제네릭을 사용하면 오히려 독이 되어 코드를 복잡하게 만든다.

