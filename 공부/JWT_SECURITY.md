# JWT 인증 및 보안 설정 정리 (Roomhub Project)

## 1. JWT (JSON Web Token) 개요
JWT는 당사자 간에 정보를 JSON 개체로 안전하게 전송하기 위한 컴팩트하고 독립적인 방식을 정의하는 개방형 표준(RFC 7519)입니다. 이 정보는 디지털 서명이 되어 있으므로 확인하고 신뢰할 수 있습니다.

### 구성 요소
1. **Header**: 토큰의 타입(JWT)과 사용 중인 서명 알고리즘(예: HMAC SHA256)을 포함합니다.
2. **Payload**: 토큰에 담을 정보(Claim)가 포함됩니다. (예: 사용자 식별자, 권한, 만료 시간 등)
3. **Signature**: 헤더의 인코딩 값과 페이로드의 인코딩 값을 합친 후, 비밀키를 이용해 생성한 서명입니다.

---

## 2. Roomhub의 보안 아키텍처
우리 프로젝트는 **Spring Security**와 **JWT**를 결합하여 **Stateless**한 인증 시스템을 구축했습니다.

### 주요 설정 (`SecurityConfig`)
- **CSRF/HTTP Basic/Form Login 비활성화**: REST API 서버이므로 세션 기반 인증이 아닌 토큰 인증을 사용하기 위해 비활성화합니다.
- **Session Policy (Stateless)**: 서버에서 세션을 유지하지 않음을 명시합니다.
- **Filter Chain**: `UsernamePasswordAuthenticationFilter` 이전에 `JwtAuthenticationFilter`를 실행하여 모든 요청에 대한 토큰 검증을 수행합니다.
- **OAuth2 Login**: Google 로그인 등 외부 인증을 연동하며, 성공 시 `OAuth2SuccessHandler`를 통해 JWT를 발급합니다.

---

## 3. JWT 인증 흐름
1. **클라이언트 요청**: HTTP Header에 `Authorization: Bearer <JWT>` 형식으로 토큰을 전달합니다.
2. **JwtAuthenticationFilter**:
    - 헤더에서 토큰을 추출(`resolveToken`).
    - `TokenProvider`를 통해 토큰의 유효성(서명, 만료시간 등)을 검증.
    - 유효한 토큰일 경우, 토큰에서 사용자 정보와 권한을 추출하여 `SecurityContextHolder`에 `Authentication` 객체를 저장합니다.
3. **Access Control**: Spring Security가 `SecurityContext`의 인증 정보를 바탕으로 해당 요청의 접근 허용 여부를 결정합니다.

---

## 4. TokenProvider의 역할
`TokenProvider`는 JWT의 생명주기를 관리하는 핵심 컴포넌트입니다.

- **토큰 생성 (`generateTokenDto`)**: Authentication 객체나 Email/Role 정보를 받아 Access Token과 Refresh Token을 생성합니다.
- **인증 정보 조회 (`getAuthentication`)**: 토큰의 페이로드를 파싱하여 Spring Security에서 사용할 수 있는 `Authentication` 객체로 변환합니다.
- **검증 (`validateToken`)**: `io.jsonwebtoken` 라이브러리를 사용하여 토큰의 위변조, 만료 여부를 체크합니다.
