---
title : "구조적 타이핑"
permalink: typescript/structural-typing
categories:
  - TypeScript
tags:
  - [TypeScript]
toc: true
toc_sticky: true
---

타입스크립트는 구조적 타이핑 특성 때문에 서로 다른 타입일지라도 구조가 같으면 같은 타입으로 인식한다.

```typescript
interface Vector2D {
  x: string;
  y: string;
}

interface Vector3D {
  x: string;
  y: string;
  z: string;
}

function testFunc(position: Vector2D) {
  console.log(`x:${position.x} -- y:${position.y}`);
}

const a: Vector2D = { x: '2DX', y: '2DY' };
testFunc(a); // Success

const b: Vector3D = { x: '3DX', y: '3DX', z: '3DZ' };
testFunc(b); // Success

// 객체 리터럴을 직접 전달하면 초과 속성 검사를 진행함.
testFunc({ x: '2DX', y: '2DY' }); // Success
testFunc({ x: '2DX', y: '2DY', z: '3DZ' }); // Fail
```