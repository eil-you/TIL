# 🏗️ [CS 정리] Jenkins (젠킨스)

## 1. Jenkins란?
**Jenkins**는 소프트웨어 개발 시 **지속적 통합(CI, Continuous Integration)**과 **지속적 배포(CD, Continuous Deployment)**를 자동화해주는 오픈소스 자동화 서버입니다. 자바 기반으로 만들어졌으며, 수많은 플러그인을 통해 빌드, 테스트, 배포 환경을 자유롭게 구성할 수 있습니다.

---

## 2. 주요 개념 및 역할

### ① CI (Continuous Integration)
*   개발자들이 각자의 코드를 공유 저장소에 병합할 때마다 배포 전 테스트와 빌드를 수행.
*   코드 충돌을 조기에 발견하고 품질을 유지할 수 있게 함.

### ② CD (Continuous Deployment)
*   빌드 및 테스트가 끝난 코드를 자동으로 운영 서버나 스테이징 환경에 반영.
*   수동 배포의 번거로움과 실수를 방지.

### ③ Pipeline (파이프라인)
*   빌드-테스트-배포 단계를 하나의 스크립트로 정의한 것.
*   **Groovy** 언어를 사용하여 `Jenkinsfile`에 정의하며, 전체 흐름을 시각적으로 관리할 수 있음.

---

## 3. Jenkins의 장점

1.  **방대한 플러그인**: 1,700개 이상의 플러그인(Git, Docker, AWS, Slack 등)을 통해 무한한 확장성 제공.
2.  **커뮤니티 지원**: 가장 오래되고 널리 쓰이는 CI 도구인 만큼 레퍼런스가 많음.
3.  **유연한 스케줄링**: 특정 시간, 코드 Push 시점, 혹은 수동 실행 등 다양한 트리거 설정 가능.
4.  **분산 빌드**: Master-Slave 구조를 통해 여러 대의 장비로 빌드 작업을 나누어 처리할 수 있음 (속도 개선).

---

## 4. 작동 프로세스 (Workflow)

1.  **Code Commit**: 개발자가 GitHub/Bitbucket 등에 코드를 푸시.
2.  **Trigger**: Jenkins가 Webhook 등을 통해 변경 사항 감지.
3.  **Job Execution**:
    *   **Clone**: 소스 코드를 가져옴.
    *   **Build**: 컴파일 및 라이브러리 로드 (Gradle, Maven 등).
    *   **Test**: JUnit 등의 테스트 코드 실행.
    *   **Analyze**: 정적 코드 분석 (SonarQube 등).
4.  **Result Notification**: 성공/실패 여부를 Slack, Email 등으로 알림.
5.  **Deployment**: 빌드 산출물을 서버에 배포 (Docker, SSH 등).

---

## 5. Jenkins vs GitHub Actions
| 특징 | Jenkins | GitHub Actions |
| :--- | :--- | :--- |
| **운영 방식** | 자체 서버 설치 (On-premise) | 클라우드 기반 (SaaS) |
| **관리 부담** | 서버 사양, 보안 등을 직접 관리 | GitHub에서 모두 관리해줌 |
| **커스터마이징** | 매우 높음 (거의 모든 것 제어 가능) | 상대적으로 제한적 (사용 편의성 중심) |
| **비용** | 무료 (서버 유지비 발생) | 일정량 무료 (초과 시 유료) |

---

## 6. 요약 및 결론
Jenkins는 **"자동화 공장의 컨베이어 벨트"**와 같습니다. 초기 설정은 복잡할 수 있으나, 한 번 구축해두면 개발자는 핵심 비즈니스 로직에만 집중할 수 있는 환경(DevOps)을 만들어줍니다.
