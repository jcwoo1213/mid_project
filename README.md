# 프로젝트명 : 발달장애인 지원 프로그램

## 개요
 - ### 01 프로젝트 개요
 - 발달 장애인 지원의 효율성과 접근성 증대를 위한 웹 기반 지원자 통합 관리 페이지
 - *·* 공간 제약 극복: 온라인으로 편리하게 지원 신청을 하여 접근성이 쉬워짐
 - *·* 업무 효능 상승: 기존의 여러 단계로 분산 된 업무를 한 페이지로 쉽게 업무가능
 - ### 02 개발동기
 - 기존의 지원 방식은 보호자 분이 집접 주민센터나 구청 등 특정장소를 방문하여 지원신청이 이루어지는데 이 방식은 효율성과 접근성이 떨어진다고 생각하여 좀 더 편하고 쉬운 방식으로 발달 장애인분들의 지원이 이루어 지면 어떨까 하는 생각으로 개발을 하게 되었습니다
 - ### 03 기대효과
 - 서비스 사각지대 극복: 제약없는 복지 서비스 접근 가능
 - 담당자와 원활한 소통 가능: 담당자가 지원자의 대한 수정이나 보안사항을 실시간 확인가능
 - 맞춤형 지원 매칭: 장애 유형 및 조건에 맞는 정확한 정보관리로 최적의 담당자 매칭 지원

## 팀원
 - 정찬우(팀장)
 - 안형주(부팀장)
 - 장수연
 - 이민호

## 개발 기간
 - 전체 개발 기간 : 2026.02.09 ~ 2026.03.13
 - 설계 기간 : 2026.02.09 ~ 2026.02.18
 - 구현 기간 : 2026.02.19 ~ 2026.03.06
 - 배포 및 테스트 : 2026.03.09 ~ 2026.03.11
 - 발표 준비 : 2026.03.11 ~ 2026.03.12
 - 프로젝트 발표 : 2026.03.13

 - ## 기술 스택
| 구분 | 기술 |
|------|------|
| Frontend | Vue 3, Vue Router, Pinia, Axios, Material Dashboard 2 |
| Backend | Node.js, Express 5 |
| Database | MariaDB / MySQL |
| 아키텍처 | routes → controller → service |

---

## 담당 업무 : [본인 이름]

### 1. 설문지 양식 관리
관리자(a4)가 지원 신청서의 **질문 구조를 버전별로 설계·등록·관리**하는 기능

**주요 기능**
- 양식 목록 조회 (버전명, 작성일, 사용 시작/종료일, 코멘트, 사용 여부)
- 신규 양식 작성 (대분류 → 소분류 → 질문 계층 구조)
- 기존 버전 불러와 수정 후 새 버전으로 등록
- 특정 버전 **사용 등록** (기존 사용 중 양식은 자동 종료)

**응답 타입**
| 코드 | 유형 |
|------|------|
| l1 | 자유 응답형 |
| l2 | 2지선다형 |
| l3 | 5지선다형 |

**관련 경로**
| 구분 | 경로 |
|------|------|
| Frontend | `/form`, `/form/write`, `/form/view/:num` |
| Backend API | `GET /api/form/list`, `POST /api/form/write`, `GET /api/form/getForm/:num`, `PATCH /api/form/usage/:ver` |

**주요 파일**
- `frontend/src/views/form/formList.vue` — 양식 목록
- `frontend/src/views/form/formWrite.vue` — 양식 작성
- `frontend/src/views/form/formView.vue` — 양식 상세·사용 등록
- `backend/controllers/form_controller.js`
- `backend/services/form_service.js`

**DB 테이블**
`form_version` → `bigcategory` → `smallcategory` → `question` → `examples`

---

### 2. 설문지 작성 · 조회
보호자/일반 회원이 **현재 사용 중인 양식**으로 지원 신청서를 작성하고, 권한에 따라 목록·상세를 조회하는 기능

**작성 (`/document/write`)**
- 로그인 사용자의 지원자 목록에서 대상 선택
- `GET /api/form/usageForm`으로 **현재 사용 중(h1)** 양식 로드
- 대분류/소분류/질문별 응답 입력 후 제출
- `POST /api/document/write`로 저장 (지원자, 작성자, 양식 버전, 응답 포함)

**조회 (`/document`)**
- 권한·센터·검색 조건(작성자, 담당자, 지원자)에 따른 신청서 목록
- 모달에서 지원신청서 응답 상세 조회
- 지원계획서·지원결과서 연동 조회

**관련 경로**
| 구분 | 경로 |
|------|------|
| Frontend | `/document`, `/document/write` |
| Backend API | `GET /api/document/list`, `POST /api/document/write`, `GET /api/document/getResp/:num` |

**주요 파일**
- `frontend/src/views/document/write_document.vue` — 설문 작성
- `frontend/src/views/list/documentLIST.vue` — 설문 목록·조회
- `backend/controllers/document_controller.js`
- `backend/services/document_service.js`

---

### 3. 담당자 관리
지원자·신청서에 **기관 담당자를 배정·변경**하는 기능

#### 3-1. 지원자 담당자 배정 (`/support/allotment`)
- 미배정 지원자 목록 조회
- 동일 센터 기관 담당자 목록 조회 (`GET /api/user/getManager/:id`)
- 담당자 선택 후 배정 (`POST /api/support/assign`)

#### 3-2. 신청서 담당자 지정 (`/work/representative`)
- 신청서 작성자 센터의 담당자·관리자 목록 조회
- 신청서별 담당자 등록·변경 (`PATCH /api/document/manager/:doc_num/:manager_id`)
- 담당자 지정 시 우선순위 상태 자동 갱신

**관련 경로**
| 구분 | 경로 |
|------|------|
| Frontend | `/support/allotment`, `/work/representative` |
| Backend API | `POST /api/support/assign`, `PATCH /api/document/manager/:doc_num/:manager_id`, `GET /api/user/getManager/:id` |

**주요 파일**
- `frontend/src/views/list/requestList.vue` — 지원자 담당자 배정
- `frontend/src/views/work/representative.vue` — 신청서 담당자 지정
- `backend/controllers/support_controller.js`
- `backend/services/support_service.js`

---

### 담당 기능 흐름도


## 커밋 메시지 형식
 - (이름 / yyyyMMdd / 작업내용 )
 - EX) 홍길동 / 202X0XXX / README.md 작성






