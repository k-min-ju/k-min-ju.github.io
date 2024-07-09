---
title : "React-Query란"
permalink: react/react-query
categories:
  - React
tags:
  - [React]
toc: true
toc_sticky: true
---

---
```
💡 fetching, caching, 서버 데이터와의 동기화를 지원해주는 라이브러리이다. 복잡하고 장황한 코드가 필요하지 않고 React Component 내부에서 간단하고 
   직관적으로 API를 사용할 수 있다.
```
<br>

# 사용하는 이유
---
```
1. React-Query를 사용함으로써 개발자는 클라이언트 상태와 서버 상태를 명확하게 분리하여 관리하고 처리할 수 있다.
2. React를 사용해서 개발하다 보면 아주 많은 상태들을 생성하고 관리하게 된다. 클라이언트 상태, 서버 상태 등을 따로 구분하지 않고 애플리케이션에 필요한 상태들을 생성한다.
3. React-Query를 사용하면 서버 상태를 생성하지 않아도 되고 isLoading과 같은 로딩 상태인 클라이언트 상태를 따로 만들지 않아도 된다. 
   React-Query가 로딩, 에러 관련 상태를 제공한다.
4. React에서 사용자가 애플리케이션에 접속 후 어떤 행동도 하지 않는 경우 서버에 데이터를 요청하거나 업데이트를 하지 않았기 때문에 서버 상태와 현재 애플리케이션에서 
   사용하는 데이터가 달라서 사용자에게 잘못된 데이터를 보여 줄 수 있다. React-Query에서는 다양한 기능을 통하여 서버 상태를 받아와 데이터를 갱신하는 기능이 있다.
5. 캐싱 기능이 있어 불필요한 서버 요청을 줄일 수 있다.
```
<br>

# 서버에서 데이터 요청하기
---
```react
const [data, setData] = useState();
const [isLoading, setIsLoading] = useState(false);

useEffect(() => {
  const getData = async () => {
    try {
      setIsLoading(false);
      const res = await axios.get("주소");
      setIsLoading(true);
      setData(res);
    } catch (e) {
      console.log(e);
    }
  };
  getData();
}, []);
```
<br>

# React-Query 사용하기
---
```react
 const getData = async () => {
    try {
      const res = await axios.get("주소");
      return res;
    } catch (e) {
      console.log(e);
    }
  };
const {isLoading, data} = useQuery(["getData"], getData);

💡 서버에 데이터를 요청하는 비동기 함수인 getData 코드를 작성하고 useQuery 2번째 인자에 
   전달해 주면 useQuery는 서버에게 요청한 데이터와 로딩, 에러 관련 등의 상태를 반환해 준다.
```
<br>

# Data Fetching 단순화
---
- 기존의 방식은 Fetching 코드를 작성하고 데이터를 담아 둘 상태 생성, useEffect를 이용해 컴포넌트 Mount시 데이터를 Fetching 한 뒤 상태에 저장하였다. 
  이는 3가지 단계로 요약할수 있다.
1. Fetching 코드 작성
2. 데이터를 담아 둘 상태 생성
3. useEffect를 이용해 컴포넌트 Mount시 데이터를 Fetching 한 뒤 상태에 저장
이 코드를 예시로 들자면

```react
import { useEffect, useState } from "react";

const getServerData = async () => {
  const data = await fetch(
    "https://jsonplaceholder.typicode.com/posts"
  ).then((response) => response.json());
  return data;
};

export default function App() {
  const [state, setState] = useState<any[]>([]);

  useEffect(() => {
    getServerData()
      .then((dataList) => setState(dataList))
      .catch((e) => setState([]));
  }, []);

  return <div>{JSON.stringify(state)}</div>;
}
```
이 과정을 useQuery한 줄로 처리할 수 있다.

```react
import { useQuery } from "@tanstack/react-query";

const getServerData = async () => {
  const data = await fetch(
    "https://jsonplaceholder.typicode.com/posts"
  ).then((response) => response.json());
  return data;
};

export default function App() {
  const { data } = useQuery(["data"], getServerData);

  return <div>{JSON.stringify(data)}</div>;
}
```
<br>

# 캐싱
> 캐싱이란 특정 데이터의 복사본을 저장하여 이후 동일한 데이터의 재접근 속도를 높히는 것. 

<br>

# staletime
💡 호출한 데이터는 React-Query 자체적으로 저장해둔다. staletime은 이 캐시데이터의 유통기한을 정해주는 옵션이다. 
   default는 0으로, 별다른 옵션 세팅을 해주지 않는다면 데이터가 stale하다고 판단하여 캐싱 기능을 이용할 수 없다.
> fresh(최신의 데이터) <br>
  stale(기존의 데이터)

<br>

# cachetime
💡 cashetime은 저장한 데이터를 얼마나 유지할지 정해주는 옵션이다.  
   default는 5분이다.

```react
const { data } = useQuery(['data', getServerData,{
  staletime: 10 * 60 * 1000;
  cachetime: 10 * 60 * 1000;
})
``` 

위와 같은 옵션을 통해 별다른 refresh가 없을 때, 10분 내 재호출시 API를 호출하지 않고 캐싱된 데이터를 제공해준다.
또한 데이터 갱신의 시점을 옵션으로 설정할 수 있는데,
1. 브라우저에 포커스가 들어온 경우
2. 새로운 컴포넌트 마운트가 발생한 경우
3. 네트워크 재연결이 발생한 경우

```
refetchOnWindowFocus, //default: true
refetchOnMount, //default: true
refetchOnReconnect, //default: true
staleTime, //default: 0
cacheTime, //default: 5분 (60 * 5 * 1000)
```

<br>

기본적으로 GET 에는 useQuery가, PUT, UPDATE, DELETE에는 useMutation이 사용된다.

# useQuery  
1. 첫 번째 파라미터로 unique key를 포함한 배열이 들어간다. 이후 동일한 쿼리를 불러올 때 유용하게 사용된다.
2. 첫 번째 파라미터에 들어가는 배열의 첫 요소는 unique key로 사용되고, 두 번째 요소부터는 query 함수 내부의 파라미터로 값들이 전달된다.
3. 두 번째 파라미터로 실제 호출하고자 하는 비동기 함수가 들어간다. 이때 함수는 Promise를 반환하는 형태여야 한다.
4. 최종 반환 값은 API의 성공, 실패 여부, 반환값을 포함한 객체이다.

```react
import {
  QueryClient,
  QueryClientProvider,
  useQuery,
} from '@tanstack/react-query'

const queryClient = new QueryClient()

export default function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Example />
    </QueryClientProvider>
  )
}

function Example() {
  const { isLoading, error, data } = useQuery({
    queryKey: ['repoData'],
    queryFn: () =>
      fetch('https://api.github.com/repos/tannerlinsley/react-query').then(
        (res) => res.json(),
      ),
  })

  if (isLoading) return 'Loading...'

  if (error) return 'An error has occurred: ' + error.message

  return (
    <div>
      <h1>{data.name}</h1>
      <p>{data.description}</p>
      <strong>👀 {data.subscribers_count}</strong>{' '}
      <strong>✨ {data.stargazers_count}</strong>{' '}
      <strong>🍴 {data.forks_count}</strong>
    </div>
  )
}
```

useQuery 함수가 반환하는 객체를 보면 isLoading 을 통해 로딩 여부를, error 를 통해 에러 발생 여부를, data를 통해 성공 시 데이터를 반환할 수 있다.
isLoading과 error를 이용하여 각 상황 별 분기를 쉽게 진행할 수 있다.  

<br>
# useQueries
여러개의 useQuery를 한 번에 실행하고자 하는 경우 기존의 Promise.all()처럼 묶어서 실행할 수 있다.
```react
const results = useQueries({
  queries: [
    { queryKey: ['post', 1], queryFn: fetchPost, staleTime: Infinity},
    { queryKey: ['post', 2], queryFn: fetchPost, staleTime: Infinity}
  ]
})

// 두 query에 대한 반환값이 배열로 묶여 반환된다
```

<br>

# useMutation
위에서 언급한 것처럼 PUT, UPDATE, DELETE 와 같이 값을 변경할 때 사용하는 API다. 반환값은 useQuery와 동일하다.

```react
function App() {
  const mutation = useMutation({
    mutationFn: (newTodo) => {
      return axios.post('/todos', newTodo)
    },
  })

  return (
    <div>
      {mutation.isLoading ? (
        'Adding todo...'
      ) : (
        <>
          {mutation.isError ? (
            <div>An error occurred: {mutation.error.message}</div>
          ) : null}

          {mutation.isSuccess ? <div>Todo added!</div> : null}

          <button
            onClick={() => {
              mutation.mutate({ id: new Date(), title: 'Do Laundry' })
            }}
          >
            Create Todo
          </button>
        </>
      )}
    </div>
  )
}
```

위의 코드에서 볼 수 있듯이 반환값은 useQuery와 동일하지만, 처음 사용 시에 post 비동기 함수를 넣어주었다.  
이때 useMutation의 첫 번째 파라미터에 비동기 함수가 들어가고, 두 번째 인자로 상황 별 분기 설정이 들어간다는 점이 차이이다.  
실제 사용 시에는 mutation.mutate 메서드를 사용하고, 첫 번째 인자로 API 호출 시에 전달해주어야하는 데이터를 넣어주면 된다.  
