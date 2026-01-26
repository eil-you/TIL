# 🐳 Docker & Docker-Compose 사용 가이드

Roomhub 프로젝트를 도커 환경에서 실행하고 관리하기 위한 유용한 명령어와 팁 모음입니다.

---

## 🚀 기본 실행 명령어

### 1. 프로젝트 빌드 및 실행
어플리케이션 수정 사항이 있을 때 이미지를 다시 빌드하며 실행합니다.
```powershell
docker-compose up --build
```

### 2. 백그라운드 실행
터미널을 점유하지 않고 백그라운드에서 실행합니다.
```powershell
docker-compose up -d
```

### 3. 서비스 정지 및 컨테이너 삭제
```powershell
docker-compose down
```

---

## 🔍 로그 및 상태 확인

### 1. 실시간 로그 확인
실행 중인 컨테이너의 로그를 실시간으로 확인합니다.
```powershell
docker-compose logs -f
```

### 2. 컨테이너 상태 확인
```powershell
docker ps
# 또는
docker-compose ps
```

---

## 🧹 데이터 초기화 및 정리

### 1. DB 데이터까지 완전히 삭제 (초기화)
설정된 볼륨(데이터베이스 저장소)까지 삭제하여 DB를 완전히 초기 상태로 되돌립니다.
```powershell
docker-compose down -v
```

### 2. 미사용 이미지/컨테이너 일괄 정리
```powershell
docker system prune -a
```

---

## 🛠 트러블슈팅 팁

### 1. 포트 충돌 해결
`Bind for 0.0.0.0:3306 failed` 에러가 발생하면, 로컬 PC에 이미 설치된 MySQL이 3306 포트를 사용 중인 경우입니다.
*   **해결**: 로컬 MySQL 서비스를 종료하거나, `docker-compose.yml`에서 DB 포트를 `3307:3306` 등으로 변경합니다.

### 2. 의존성 문제 (DB 실행 전 App 실행)
현재 `docker-compose.yml`에 `healthcheck` 설정이 되어 있어, MySQL이 완전히 준비된 후에 Spring 부트가 연동되도록 구성되어 있습니다. 만약 앱이 DB 연결 실패로 종료된다면 `docker-compose up`을 다시 시도해 보세요.

### 3. JAR 파일 수정 반영이 안 될 때
코드를 수정했는데 도커에 반영이 안 된다면 `--build` 옵션을 명시했는지 확인하세요.
```powershell
docker-compose up --build --force-recreate
```

---

## 📁 주요 파일 정보
*   **Dockerfile**: Java 21 환경에서 전체 프로젝트를 빌드하고 `user-service` jar를 실행하도록 설정됨.
*   **docker-compose.yml**: MySQL 8.1과 Spring Boot 앱을 컨테이너 네트워크로 연결함. (앱은 `db:3306` 주소로 DB에 접속)
