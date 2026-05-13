---
title: 이펙트용 메쉬, 모든 면을 동일한 UV 크기로 만드는 법
description: ""
pubDate: 2026-05-14
tags:
  - UV
  - 3D
  - 3dsmax
  - UVWMap
category: 3ds Max
share: true
heroImage: ""
---
# 이펙트용 메쉬, 모든 면을 동일한 UV 크기로 만드는 법

## 가장 쉬운 비유

3D 오브젝트에 텍스처를 붙이는 건 **선물 포장**과 같다.

선물 상자(3D 오브젝트)에 포장지(텍스처)를 붙일 때
어떤 방향으로, 어떤 크기로 붙일지 정해주는 게 **UVW Map**이다.

---

## U, V, W 가 뭔가?

3D 공간에 X, Y, Z 축이 있듯이
텍스처 공간에는 **U, V, W** 축이 있다.

- **U** = 텍스처의 가로 방향
- **V** = 텍스처의 세로 방향
- **W** = 텍스처의 깊이 방향 (거의 안 씀)

---

## 매핑 타입 종류

| 타입 | 모양 | 언제 쓰나 |
|------|------|----------|
| **Planar** | 평면 | 벽, 바닥처럼 평평한 면 |
| **Box** | 박스 | 건물, 상자 같은 육면체 |
| **Cylindrical** | 원통 | 기둥, 캔, 나무 |
| **Spherical** | 구 | 공, 행성 |
| **Face** | 면 개별 | 이펙트, 각 면에 동일한 텍스처 |

---

## Face 매핑의 원리

**Face 모드는 면 하나하나에 텍스처를 개별로 씌운다.**

각 면이 아무리 크거나 작아도, 길거나 짧아도
→ **그 면의 크기 = UV 0~1 전체** 로 강제 매핑된다.

각 면에 **스티커를 하나씩 붙이는 것**과 같다.

---

## 이펙트에서 왜 유용한가

충격파 이펙트 쉐이더에서 UV 좌표를 이용해
텍스처를 흘리거나 왜곡시킬 때

모든 면의 UV가 **0~1로 통일**되어 있어야
각 면에서 **동일한 애니메이션**이 나온다.

면마다 UV 크기가 다르면
텍스처가 면마다 다르게 보이거나 끊겨 보인다.

---

## 이펙트 작업 흐름

1. Line으로 경로 만들기
2. Extrude로 면 만들기
3. **UVW Map → Face** 적용
4. 게임 이펙트 쉐이더에 넣기

---

## 삽질 기록

목표는 단순했다.
**모든 면의 UV를 동일한 정사각형 크기로 0~1에 맞추기**

### 시도 1 - Rescale Clusters + Pack
Unwrap UVW에서 Rescale Clusters → Pack 시도
→ 면 크기 비율대로 UV가 제각각으로 나옴 ❌

### 시도 2 - MAXScript 도전
존재하지 않는 함수들을 계속 시도했다.

```maxscript
uvwMod.fitMap()         -- 없는 함수 ❌
uvwMod.normalizeMap()   -- 없는 함수 ❌
uvwMod.numberFaces()    -- 없는 함수 ❌
getMethodNames uvwMod   -- 없는 함수 ❌
showMethods uvwMod      -- 없는 함수 ❌
```

### 시도 3 - MAXScript 계속 시도

```maxscript
uvMod.unwrap2.pack ...  -- unwrap2가 undefined ❌
polyop.getNumFaces()    -- undefined ❌
obj.numfaces            -- undefined ❌
```

### 시도 4 - Flatten Mapping
Mapping → Flatten Mapping 시도
→ 면의 실제 크기 비율이 그대로 반영되어 크기가 다름 ❌

### 최종 해결 - UVW Map → Face ✅

Unwrap UVW를 버리고 **UVW Map 모디파이어**로 전환
Mapping 타입을 **Face** 로 설정하는 것만으로 해결됐다.

---

## 결론

> Unwrap UVW는 복잡한 캐릭터 UV 작업에 적합하다.
> 이펙트처럼 **모든 면을 동일하게 매핑**할 때는
> **UVW Map → Face** 가 정답이다.