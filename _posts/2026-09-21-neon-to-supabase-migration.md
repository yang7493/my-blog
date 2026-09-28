---
layout: post
title: "DB가 막히면 '무중단'이 아니라 '무엇을 포기할지'부터 정해야 한다"
date: 2026-09-21 18:00:00 +0900
categories: [Backend]
tags: [postgresql, supabase, db-migration, session, documentation]
mermaid: true
---

## 들어가며 (Situation)

15일차부터 실제 데이터를 쌓기 시작한 **Neon Postgres**가, 실사용이 늘면서 요청량 초과로 완전히 정지되는 사태를 맞았다.
데이터를 옮기고 말고 할 것도 없이 **DB 자체에 접속이 안 되는** 상황이었다. 주말(9/19~20)을 지나 월요일인 오늘, 가장 먼저 이 문제부터 해결해야 했다.

---

## 문제 상황 (Task)

### 🚨 원본에 접근할 수 없는 마이그레이션

Neon 계정이 요청량 초과로 **compute 자체가 정지**되어, 기존 데이터를 꺼낼 수조차 없는 상태였다.
보통의 DB 마이그레이션이라면 "기존 데이터를 새 DB로 옮기고 트래픽을 전환"하는 순서를 밟지만, 이번엔 원본에 접근할 수 없으니 **데이터 이전이라는 선택지 자체가 없었다.**

### 함께 드러난 잠재 버그

그리고 이전부터 잠재해 있던 버그도 함께 드러났다.

DB 마이그레이션이나 탈퇴 등으로 **세션 쿠키의 유저 id가 실제 `users` 테이블에 없어졌는데도, JWT 서명 자체는 유효해서** 서버가 여전히 로그인 상태로 취급하고 있었다.
그 상태에서 글쓰기·좋아요처럼 그 id를 외래키(FK)로 쓰는 동작을 하면 제약 위반으로 **500 에러**가 났다.

배포 설정 파일(`vercel-deployments.json`)이 실수로 저장소에 커밋되어 있던 것도 뒤늦게 발견됐다.

---

## 해결 과정 (Action)

### A. Neon에서 Supabase로 — "스키마만 옮기고 데이터는 새로 쌓기"

> 💡 데이터를 꺼낼 수 없으니, 목표를 **"무중단 이전"에서 "스키마 구조는 그대로 유지한 채 새 DB에서 다시 시작"** 으로 조정했다.
> 과거 데이터 소실은 감수하는 대신 서비스를 계속 이어갈 수 있는 쪽을 택한 것이다.

```mermaid
flowchart TD
    A["Neon Postgres<br/>@neondatabase/serverless (neon-http)"] -->|"요청량 초과로 compute 정지"| B["접근 불가<br/>데이터 추출 불가능"]
    B --> C{"목표 재설정"}
    C -->|"포기"| D["과거 데이터 전량"]
    C -->|"유지"| E["스키마 구조"]
    E --> F["Supabase Postgres<br/>postgres(postgres-js) / pg 드라이버"]
    F --> G["Supavisor pooler 호환<br/>prepare:false, ssl:'require'"]
    F --> H["drizzle-kit push는<br/>DATABASE_URL_UNPOOLED 우선"]
    F --> I["destinations 큐레이션 데이터만 재시드<br/>(routes 사전계산은 다음으로)"]
```

작업한 내용은 네 가지다.

1. 드라이버를 `@neondatabase/serverless`(neon-http)에서 **`postgres`(postgres-js) / `pg`** 로 교체했다.
2. Supabase의 커넥션 풀러(**Supavisor**)와 호환되도록 `prepare:false`, `ssl:'require'` 옵션을 적용했다.
3. 스키마를 실제로 반영하는 `drizzle-kit push`는 풀링된 연결 대신 **`DATABASE_URL_UNPOOLED`(non-pooling 연결)를 우선** 쓰도록 했다 — 스키마 변경(DDL)은 풀러를 거치면 예기치 않게 실패하는 경우가 있어서다.
4. 데이터 중에서 서비스 운영에 꼭 필요한 **`destinations`(여행지 큐레이션)** 만 새로 시드했고, 경로 사전계산(`routes`) 데이터는 서비스 필수 요소가 아니라고 판단해 다음으로 미뤘다 — 전부를 한 번에 복구하려 하지 않고 **우선순위를 나눈 것**이다.

> **용어 카드 — 커넥션 풀링과 pooler**
>
> 서버리스 환경은 요청마다 함수가 새로 실행될 수 있어서, DB 연결을 매번 새로 맺으면 **연결 수가 금방 한계에 도달**한다. Supavisor 같은 pooler는 여러 요청이 소수의 실제 DB 연결을 공유하도록 중개해준다.
>
> 다만 pooler를 거치면 일부 세션 단위 기능(예: DDL, prepared statement)이 제한될 수 있어서, 스키마 변경처럼 민감한 작업은 pooler를 거치지 않는 연결(**UNPOOLED**)을 따로 쓴다.

### B. 탈퇴한 유저의 세션을 더 이상 신뢰하지 않도록

🔴 **JWT 서명이 유효하다고 해서 그 유저가 실제로 존재한다는 보장은 아니었다.**

🟢 `getSessionUser`가 **매 요청마다 DB로 해당 유저가 실제로 존재하는지 확인**하도록 바꿨다. 존재하지 않으면 쿠키를 지우고 **401(미인증)** 로 처리한다.

| 이전 | 이후 |
|------|------|
| JWT 서명만 유효하면 로그인 상태로 취급 | 매 요청마다 DB에서 유저 존재 여부 확인 |
| 삭제된 유저 id로 글쓰기/좋아요 시도 → FK 제약 위반 **500 에러** | 존재하지 않으면 쿠키 삭제 후 **401** |

> ⚠️ 매 요청마다 DB를 확인하는 만큼 약간의 지연이 늘어나지만, 삭제된 계정으로 인한 500 에러(사용자 입장에서는 원인을 알 수 없는 오류)보다는 안전한 쪽을 택했다.

### C. 실수로 커밋된 배포 설정 제거, README를 실측 기반으로

- `vercel-deployments.json`이 저장소에 올라가 있던 것을 발견하고 제거한 뒤 `.gitignore`에 추가했다. 배포 관련 설정 파일이 저장소에 남아 있으면 안 된다는 걸 **실제 사례로 확인**한 셈이다.
- README의 "협업 방식"·"데모" 섹션을 그동안 말로만 채워뒀던 부분에서 벗어나, `pinder`·`hello-planning` 두 저장소의 **`git log`·diff와 GitHub Issues/PR API를 직접 조회한 값**으로 다시 채웠다.
  브랜치 전략·이슈·PR·역할 분담·충돌 사례를 실제 데이터 기반으로 서술했고, 데모 섹션도 Vercel 배포 링크와 `vercel env pull` 기반 로컬 실행 방법, 실제 기능 흐름 순서로 다시 썼다.

---

## 결과 (Result)

| 항목 | Before | After |
|------|--------|-------|
| DB | Neon compute 정지 → 접속 불가 | Supabase로 이전, 서비스 재개 |
| 과거 데이터 | — | **전량 소실**(감수), 스키마와 `destinations`는 유지 |
| 삭제된 계정 세션 | FK 제약 위반 **500 에러** | 쿠키 삭제 후 **401** |
| 배포 설정 파일 | 저장소에 커밋됨 | 제거 + `.gitignore` 등록 |
| README 협업·데모 | 말로만 쓴 설명 | `git log`·PR API로 **검증한 설명** |

- Neon에서 완전히 막혔던 DB를 Supabase로 옮겨 **서비스를 다시 운영할 수 있는 상태로 되돌렸다.** 과거 데이터 전량은 소실됐지만, 스키마와 서비스 필수 데이터(`destinations`)는 유지했다.
- 탈퇴한 유저의 세션으로 인한 500 에러를 401로 정상 처리되도록 바꿔, **삭제된 계정과 관련된 예외 상황을 명확히 구분**했다.
- 실수로 커밋된 배포 설정 파일을 제거하고 `.gitignore`로 재발을 막았다.
- README의 협업·데모 섹션을 "그럴듯하게 쓴 설명"에서 **"실제 git/PR 데이터로 검증한 설명"** 으로 바꿨다.

가장 크게 배운 것은 제목 그대로다. 장애 앞에서 **"어떻게 하나도 안 잃고 옮길까"를 붙잡고 있으면 아무것도 못 한다.** 원본에 접근조차 못 하는 상황에서는 **무엇을 포기할지 먼저 정하는 것**이 실제로 서비스를 살리는 결정이었다.

---

## 더 학습하면 좋은 개념

- **커넥션 풀링과 트랜잭션 모드(Supavisor, PgBouncer)** — 왜 `prepare:false`가 필요한지는 "트랜잭션 모드 풀러는 prepared statement를 지원하지 않는다"는 사실에서 나온다. 풀러의 세 가지 모드(session / transaction / statement) 차이를 알면 이런 옵션이 주문이 아니라 논리로 읽힌다.
- **JWT의 무상태성과 토큰 무효화(revocation)** — JWT는 서명만 검증하므로 "서버가 모르는 사이 무효가 된 토큰"을 걸러내지 못한다. 짧은 만료 시간 + 리프레시 토큰, 블랙리스트, 이번처럼 매 요청 DB 확인 등 각 전략의 비용과 효과를 비교해 볼 만하다.
- **DB 마이그레이션 전략(blue-green, dual-write, 백업·복구)** — 이번엔 원본에 접근할 수 없어 선택지가 없었지만, 정상 상황에서 무중단 이전을 하려면 어떤 절차가 필요한지 알아야 다음엔 이 사태 자체를 피할 수 있다.
- **비밀·설정 파일 관리와 `.gitignore`** — 이미 커밋된 파일은 `.gitignore`에 추가해도 **히스토리에는 남는다.** 민감한 값이 들어 있었다면 키 교체까지 해야 한다는 점을 함께 알아 두면 좋다.
- **서버리스 환경의 DB 연결 한계** — 요청마다 함수가 새로 뜨는 구조에서 왜 연결 수가 폭발하는지, 그래서 왜 풀러가 사실상 필수인지가 이번 장애의 근본 배경이다.

---

## 참고 자료

- [Supabase Docs - Connect to your database](https://supabase.com/docs/guides/database/connecting-to-postgres)
- [Supabase Docs - Connection pooling and limits](https://supabase.com/docs/guides/database/connecting-to-postgres/pooling-and-limits)
- [Supabase Docs - Connection management](https://supabase.com/docs/guides/database/connection-management)
- [Drizzle ORM - Connect Supabase](https://orm.drizzle.team/docs/connect-supabase)
- [Drizzle ORM - Drizzle with Supabase Database](https://orm.drizzle.team/docs/tutorials/drizzle-with-supabase)
- [RFC 7519 - JSON Web Token (JWT)](https://datatracker.ietf.org/doc/html/rfc7519)
- [Git - gitignore 문서](https://git-scm.com/docs/gitignore)
