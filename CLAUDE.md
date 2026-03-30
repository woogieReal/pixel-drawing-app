# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 프로젝트 개요

실시간 협업 픽셀 아트 드로잉 웹 애플리케이션. `fe/`와 `api/`는 독립적인 Git 서브모듈이다.

- **Frontend**: React 19, Vite, TypeScript, Socket.IO Client (`fe/` 서브모듈)
- **Backend**: NestJS 11, PostgreSQL 16, TypeORM, Socket.IO (`api/` 서브모듈)

## 개발 명령어

### 전체 스택 실행 (Docker)
```bash
docker-compose up -d --build    # 전체 스택 시작 (postgres:5432, api:3100, fe:3000)
docker-compose down             # 종료
```

### 프론트엔드 (`fe/`)
```bash
npm run dev       # Vite 개발 서버
npm run build     # TypeScript 컴파일 + Vite 빌드
npm run lint      # ESLint
```

### 백엔드 (`api/`)
```bash
npm run start:dev    # watch 모드로 실행
npm run build        # NestJS 컴파일
npm run lint         # ESLint (자동 수정)
npm run format       # Prettier
npm run test         # Jest 단위 테스트
npm run test:watch   # Jest watch 모드
npm run test:cov     # 커버리지 포함 테스트
npm run test:e2e     # E2E 테스트 (test/ 디렉토리)
```

### 단일 테스트 실행
```bash
# api/ 디렉토리에서
npx jest --testPathPattern="canvas.service"   # 특정 파일
npx jest --testNamePattern="should create"    # 특정 테스트명
```

## 아키텍처

### 데이터 흐름
```
Browser → React (Canvas API + Socket.IO) ↔ NestJS (Gateway + Controller) → PostgreSQL
```

### 백엔드 구조 (`api/src/canvas/`)
- **`canvas.controller.ts`** — REST API: `GET /canvas` (페이지네이션), `POST /canvas`, `GET /canvas/:id`, `DELETE /canvas/:id`, `PATCH /canvas/:id/resize`
- **`canvas.gateway.ts`** — WebSocket `/canvas` 네임스페이스. 클라이언트 `draw` 이벤트 수신 → 룸 내 다른 클라이언트에 `pixelUpdated` 브로드캐스트
- **`canvas.service.ts`** — 핵심 비즈니스 로직: 인메모리 캐시, DB 플러시 (1초 간격), 썸네일 생성 (30초 간격 + 마지막 사용자 퇴장 시 즉시)

### 실시간 드로잉 프로토콜
클라이언트가 `draw` 이벤트로 **5바이트 이진 패킷** 전송:
```
[x(1B)] [y(1B)] [r(1B)] [g(1B)] [b(1B)]
```
서버는 `pixelUpdated` 이벤트로 동일한 5바이트 패킷을 룸 내 다른 클라이언트에 브로드캐스트.

### 캐싱 전략
- `canvasCache: Map<number, Canvas>` — 인메모리 캐시
- `dirtyCanvasIds: Set<number>` — 변경된 캔버스 추적
- 1초마다 dirty 캔버스를 DB에 flush
- 30초마다 dirty 썸네일 재생성 (최대 64×64 다운샘플)

### DB 스키마 (`canvases` 테이블)
- `pixel_data` — `bytea` 타입, RGB 버퍼 (width × height × 3 bytes)
- `thumbnail` — `bytea` 타입, nullable, 최대 64×64 RGB 다운샘플

### 프론트엔드 구조 (`fe/src/`)
- **`App.tsx`** — React Router: `/` (홈), `/canvas` (목록), `/canvas/:id` (드로잉)
- **`pages/CanvasListPage.tsx`** — 썸네일 갤러리, 페이지네이션, FAB 생성 버튼
- **`pages/CanvasDetailRoomPage.tsx`** — 실시간 드로잉 캔버스 (색상 팔레트, 줌, 리사이즈)

## 환경 변수

백엔드 (`api/`):
- `DB_HOST`, `DB_PORT`, `DB_USERNAME`, `DB_PASSWORD`, `DB_DATABASE`
- `CORS_ORIGIN` (기본값: `http://localhost:3000`)

프론트엔드 (`fe/`):
- `VITE_API_URL` (기본값: `http://localhost:3100`)

## 서브모듈 관리
`fe/`와 `api/`는 독립적인 Git 저장소다. 각 서브모듈 내에서 변경 후 해당 저장소에 커밋/푸시하고, 루트에서 서브모듈 포인터를 업데이트해야 한다.
```bash
git submodule update --init --recursive   # 서브모듈 초기화
```
