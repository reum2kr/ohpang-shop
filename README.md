# Oh!Pang — 쇼핑몰 프론트엔드

OWASP Top 10:2025 웹 취약점 분석 프로젝트의 **프론트엔드**입니다. 일반 온라인 쇼핑몰처럼 회원가입·로그인·상품 검색·장바구니·주문·모의 결제가 가능하며, 백엔드 REST API와 연동하도록 설계되었습니다.

> 역할 분담상 **취약점 구현·검증은 백엔드(로컬 실습 환경)** 에서 담당합니다. 이 프론트엔드에는 공격 기능이나 의도적 취약점이 없습니다.

## 구성

- `index.html` (HTML/CSS/JS 인라인, 로고 이미지 내장) + `images/` (상품 사진 p101~p120.jpg) → GitHub Pages에 그대로 배포
- 백엔드: [Soyoon0608/ohpang-backend](https://github.com/Soyoon0608/ohpang-backend) (Node.js / Express, Supabase) — 로컬 `127.0.0.1:3000`
- 백엔드가 없는 곳(GitHub Pages 등)에서는 **목(mock) 데이터**로 동작합니다. 브라우저 `localStorage`에 회원/주문/장바구니가 저장돼 흐름을 끝까지 체험할 수 있습니다.

## 페이지 / 기능

| 페이지 | 기능 |
|---|---|
| 메인 | 배너, 추천 상품, 카테고리 |
| 회원가입 | 이메일·비밀번호·이름 |
| 로그인 | 이메일·비밀번호, 로그인 상태에 따른 헤더 변경 |
| 상품 목록/검색 | 검색창, 카테고리 필터, 상품 카드(이미지·가격·재고) |
| 상품 상세 | 이미지, 가격, 수량 선택, 장바구니 담기 |
| 장바구니 | 수량 변경, 삭제, 합계, 주문하기 |
| 주문서 | 주문 상품, 배송지 입력, 최종 금액 |
| 결제 | 결제 예정 금액, 결제 수단 선택, 모의 결제 |
| 결제 결과 | 성공 / 실패 화면 |
| 마이페이지 | 회원 정보, 주문 내역 |
| 주문 상세 | 주문번호·상품·결제금액·배송지, 배송지 변경 |

목 모드 체험용 계정: **demo@ohpang.shop / demo1234** (또는 직접 회원가입). 실제 백엔드 모드에서는 회원가입 후 로그인하세요.

## 백엔드 연동

### 모드 자동 선택

| 여는 방법 | 모드 |
|---|---|
| `http://localhost:…` 또는 `http://127.0.0.1:…` (예: VS Code Live Server) | **실제 백엔드 API** |
| GitHub Pages, 파일 더블클릭 등 그 외 | **목(mock) 데이터** |
| 주소 뒤에 `?api=real` / `?api=mock` | 해당 모드로 강제 |

예) `http://127.0.0.1:5500/index.html?api=mock`, `https://reum2kr.github.io/ohpang-shop/?api=real`
화면 맨 아래에 현재 모드가 표시됩니다.

### 실제 백엔드로 실행하기

1. 백엔드 저장소를 실행합니다 (`127.0.0.1:3000`).
2. 이 저장소 폴더를 로컬 웹서버로 엽니다. 예: VS Code **Live Server** → `http://127.0.0.1:5500/index.html`
3. 백엔드가 꺼져 있으면 화면에 "백엔드 서버에 연결할 수 없습니다" 안내가 뜹니다.

- 다른 PC에서 백엔드를 띄웠다면 `index.html`의 `CONFIG.API_BASE` 한 줄만 그 주소로 바꾸면 됩니다.
- 인증은 서버 세션 쿠키(`credentials: 'include'`)를 씁니다. 백엔드 CORS에서 프론트 주소(예: `http://127.0.0.1:5500`)와 credentials를 허용해야 합니다.

### 연동 규칙 (백엔드 연동본 기준)

- 응답: 인증(`register`·`login`·`me`)은 `user` 필드, 상품·주문 조회는 `data` 필드
- 상품 번호: DB 상품 id **6~25** ↔ 화면 상품 번호 **101~120** (`DB_ID_OFFSET = 95`). 사진(`images/p101~p120.jpg`)과 배너·광고 링크는 화면 번호 기준이고, 변환은 `index.html`의 API 계층에서만 합니다. DB 상품 번호가 바뀌면 `DB_ID_OFFSET`만 고치면 됩니다.
- 결제수단: `CARD` / `BANK_TRANSFER` / `KAKAO_PAY`
- 장바구니는 모드별로 따로 저장됩니다 (`ohpang_cart` / `ohpang_cart_real_v1`).

| HTTP | API | 기능 |
|---|---|---|
| POST | `/auth/register` | 회원가입 |
| POST | `/auth/login` | 로그인 |
| POST | `/auth/logout` | 로그아웃 |
| GET | `/auth/me` | 로그인 상태 확인 (새로고침 시 세션 확인) |
| GET | `/products?search=&category=` | 상품 목록·검색 |
| GET | `/products/:id` | 상품 상세 |
| POST | `/orders` | 주문 생성 (items: productId·quantity만 전송, 가격은 서버가 계산) |
| GET | `/orders` | 내 주문 내역 |
| GET | `/orders/:id` | 주문 상세 |
| PATCH | `/orders/:id/address` | 배송지 변경 |
| POST | `/orders/:id/pay` | 모의 결제 |
| GET | `/orders/:id/payment` | 결제 결과 조회 |

## GitHub Pages 배포

1. 저장소에 `index.html`, `images/` 폴더(와 `README.md`)를 올립니다.
2. Settings → Pages → Source를 `Deploy from a branch`, `main` / `/(root)`로 설정.
3. `https://<사용자명>.github.io/<저장소명>/` 에서 접속됩니다.
