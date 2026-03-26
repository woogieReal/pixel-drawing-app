# 🎨 Pixel Drawing App

빠르고 혁신적인 실시간 픽셀 아트 드로잉 협업 웹 애플리케이션입니다. 
본 프로젝트는 **Nest.js와 WebSocket 통신에 대한 학습을 주된 목적**으로 시작되어 개발되었습니다. 수많은 유저가 동시에 웹 환경에 접속해 함께 픽셀을 그리고 캔버스의 크기를 자유롭게 조절하며 실시간으로 결과를 공유할 수 있습니다.

이 저장소는 프론트엔드(`fe`)와 백엔드 API(`api`) 서브모듈 코드를 모두 포함하고 있으며, `docker-compose`를 사용하여 복잡한 환경 설정 없이 손쉽게 전체 시스템 스택을 실행할 수 있도록 구성되어 있습니다.

---

## ✨ 주요 기능 (Features)

- **캔버스 갤러리**: 효율적인 페이지네이션을 지원하여 스크롤 및 로드 모어 형태의 캔버스 목록을 탐색하실 수 있습니다.
- **실시간 드로잉 (WebSocket)**: WebSocket 통신을 기반으로, 사용자의 드로잉 액션이 최소한의 딜레이(지연시간)로 다른 참여자들의 캔버스에 즉각 브로드캐스팅 및 동기화됩니다.
- **캔버스 크기 조절**: 그림 그리기에 공간이 부족해지면 캔버스의 상/하/좌/우 4개의 모서리 방향으로 1픽셀 단위 무중단 확장이 가능합니다.
- **다양한 색깔 및 줌 뷰포트**: 6가지의 기본 색상 팔레트를 지원하며, 복잡한 픽셀 조작을 돕기 위해 돋보기 뷰포트(Zoom In / Out) 및 그리드 오버레이를 제공합니다.

---

## 🚀 테크 스택 (Tech Stack)

### **Frontend (`fe`)**
- **Framework & Runtime**: React (v19), Vite (v8)
- **Language**: TypeScript
- **Socket**: Socket.IO Client
- **Styling**: Vanilla CSS, Lucide React 아이콘

### **Backend (`api`)**
- **Framework & Runtime**: NestJS (v11), Node.js
- **Database**: PostgreSQL (TypeORM 연동)
- **Language**: TypeScript
- **Socket**: Socket.IO (NestJS WebSockets 모듈 활용)

---

## 🐳 Docker를 활용한 통합 실행 방법

전체 애플리케이션 아키텍처(프론트엔드 앱, 백엔드 API 서버, PostgreSQL 컨테이너)는 Docker Compose를 활용하여 단일 명령어로 손쉽게 배포 및 실행할 수 있습니다. 각 하위 모듈(`fe`, `api`)에서 개별적으로 구동하지 않고 통합 실행을 권장합니다.

### 1. 전제 조건 (Prerequisites)
- [Docker](https://www.docker.com/) 시스템 설치 완료
- [Docker Compose](https://docs.docker.com/compose/) 설치 완료

### 2. 프로젝트 통합 실행 방법
시스템 터미널을 열고 본 프로젝트 폴더의 루트 디렉토리(`pixel-drawing-app`)로 이동한 후 아래 명령어를 입력하여 빌드 및 구동을 시작합니다.
```bash
docker-compose up -d --build
```
> 초기 빌드 시 약간의 시간 소요가 있을 수 있습니다.

### 3. 애플리케이션 접속 방법
서버가 성공적으로 구동된 후에는 다음 주소를 통해 접속하실 수 있습니다.
- **Frontend (Web UI)**: 브라우저에서 [http://localhost:3000](http://localhost:3000) 접속
- **Backend API**: [http://localhost:3100](http://localhost:3100) (기본 REST API 및 WebSocket 통신 목적)

### 4. 서비스 종료 방법
개발 및 테스트 종료 후 컨테이너들을 멈추고 싶다면 다음 명령어를 사용하세요.
```bash
docker-compose down
```

---

## 📖 하위 모듈 개별 문서 가이드

각 세부 파트별(FE/BE) 디테일한 구현 스펙과 코드 구조 등에 대한 설명은 아래 문서를 참고하세요.
- 👉 [Frontend (FE) README 문서](./fe/README.md)
- 👉 [Backend (API) README 문서](./api/README.md)
