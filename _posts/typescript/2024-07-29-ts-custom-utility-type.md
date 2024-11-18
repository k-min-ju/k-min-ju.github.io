---
title : "커스텀 유틸리티 타입 활용하기"
permalink: typescript/custom-utility-type
categories:
  - TypeScript
tags:
  - [TypeScript]
toc: true
toc_sticky: true
---

타입스크립트로 프로젝트를 진행하다 보면 표현하기 힘든 타입을 마주할 때가 있다.  
원하는 타입을 정확하게 설정해야만 해당 컴포넌트, 함수의 안정성과 사용성을 높일 수 있지만 타입스크립트에서 제공하는 유틸리티 타입만으로는 표현하는 데 한계를 느끼기도 한다.  
이럴 때 커스텀 유틸리티 타입을 제작해서 사용하면 된다. 

<br>

> 유틸리티 함수를 활용해 styled-componenets의 중복 타입 선언 피하기 

styled-components로 만든 컴포넌트에 넘겨주는 타입은 props에서 받은 타입과 동일할 때가 대부분이다.  
Pick, Omit같은 유틸리티 타입을 활용해서 코드를 간결하게 작성할 수 있다.  

<br>

> Props 타입과 styled-componets 타입의 중복 선언 및 문제점
> 아래 컴포넌트는 수평선을 그어주는 Hr 컴포넌트다.

```tsx
export type Props = {
  height?: string;
  color?: keyof typeof colors;
  isFull?: boolean;
}

export function Hr({ height, color, isFull }: Props) {
  return <HrComponent height={height} color={color} isFull={isFull} />; 
}

//style.ts
import { Props } from '...';
type StyleProps = Pick<Props, "height" | "color">;
const HrComponent = styled.hr<StyleProps>`
  height: ${({ height }) => height || "10px"};
  margin: 0;
  background-color : ${({ color }) => colors[color || "gray7"]};
```

이처럼 Pick 유틸리티 타입을 활용해서 props에서 필요한 부분만 선택하여 styled-components 컴포넌트 타입을 정의하면, 중복된 코드를 작성하지 않아도 되고 
유지보수를 더욱 편리하게 할 수 있게 된다.

