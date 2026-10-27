# Oh!Pang — 쇼핑몰 프론트엔드

OWASP Top 10:2025 웹 취약점 분석 프로젝트의 **프론트엔드**입니다. 일반 온라인 쇼핑몰처럼 회원가입·로그인·상품 검색·장바구니·주문·모의 결제가 가능하며, 백엔드 REST API와 연동하도록 설계되었습니다.

> 역할 분담상 **취약점 구현·검증은 백엔드(로컬 실습 환경)** 에서 담당합니다. 이 프론트엔드에는 공격 기능이나 의도적 취약점이 없습니다.

## 구성

- `index.html` (HTML/CSS/JS 인라인, 로고 이미지 내장) + `images/` (상품 사진 p101~p120.jpg) → GitHub Pages에 그대로 배포
- 백엔드 API가 준비되기 전에도 동작하도록 **목(mock) 데이터**로 구동됩니다. 브라우저 `localStorage`에 회원/주문/장바구니가 저장돼 흐름을 끝까지 체험할 수 있습니다.

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

체험용 계정: **demo@ohpang.shop / demo1234** (또는 직접 회원가입)

## 백엔드 연동

`index.html` 상단 `CONFIG`에서 전환합니다.

```js
const CONFIG = {
  API_BASE: 'http://localhost:3000/api/v1', // 백엔드 기본 주소
  USE_MOCK: true,  // true: 목 데이터 / false: 실제 API 호출
};
```

- `USE_MOCK = false`로 바꾸면 아래 REST 엔드포인트(명세 기준)를 그대로 호출합니다. 인증은 쿠키 세션(`credentials: 'include'`) 기준으로 작성돼 있어, 필드명·인증 방식이 확정되면 `api` 객체만 맞추면 됩니다.

| HTTP | API | 기능 |
|---|---|---|
| POST | `/auth/register` | 회원가입 |
| POST | `/auth/login` | 로그인 |
| POST | `/auth/logout` | 로그아웃 |
| GET | `/auth/me` | 로그인 상태 확인 |
| GET | `/products?search=&category=` | 상품 목록·검색 |
| GET | `/products/:id` | 상품 상세 |
| POST | `/orders` | 주문 생성 (items: productId·quantity만 전송, 가격은 서버가 계산) |
| GET | `/orders` | 내 주문 내역 |
| GET | `/orders/:id` | 주문 상세 |
| PATCH | `/orders/:id/address` | 배송지 변경 |
| POST | `/orders/:id/pay` | 모의 결제 |
| GET | `/orders/:id/payment` | 결제 결과 조회 |

> 모든 응답은 `{ success, data }` 형태를 가정합니다. 세부 필드·상태 코드는 백엔드와 협의 후 확정하면 됩니다.

## GitHub Pages 배포

1. 저장소에 `index.html`, `images/` 폴더(와 `README.md`)를 올립니다.
2. Settings → Pages → Source를 `Deploy from a branch`, `main` / `/(root)`로 설정.
3. `https://<사용자명>.github.io/<저장소명>/` 에서 접속됩니다.
