# GitHub Actions (Git CI/CD의 중심)

GitHub Actions는 GitHub 레포지토리에서 바로 소프트웨어 개발 워크플로우를 자동화할 수 있게 해주는 도구입니다. Git의 특정 이벤트(Push, Pull Request 등)에 반응하여 CI/CD 파이프라인을 실행합니다.

---

## 1. 핵심 구성 요소

### Workflow
- 하나 이상의 실행 가능한 자동화 프로세스입니다.
- `.github/workflows` 폴더 안에 **YAML** 파일로 정의합니다.

### Event
- 워크플로우를 실행시키는 특정 활동입니다.
  - 예: `push`, `pull_request`, `issue creation` 등.

### Jobs
- 동일한 Runner(실행기)에서 실행되는 일련의 **Steps** 집합입니다.
- 기본적으로 여러 Job은 병렬로 실행되지만, 의존성을 설정하여 순차 실행도 가능합니다.

### Steps
- Job 내에서 순차적으로 실행되는 개별 작업입니다. 커맨드를 실행하거나 **Action**을 호출합니다.

### Actions
- 워크플로우에서 자주 반복되는 작업을 정의해놓은 독립적인 애플리케이션입니다. (예: 코드 체크아웃, 자바 설치 등)

---

## 2. GitHub Actions 예시 (Java/Spring Boot)

`.github/workflows/ci.yml` 파일 예시:

```yaml
name: Java CI with Gradle

on:
  push:
    branches: [ "main" ] # main 브랜치에 push될 때 실행
  pull_request:
    branches: [ "main" ] # main 브랜치로 PR이 날아올 때 실행

jobs:
  build:
    runs-on: ubuntu-latest # 실행 환경 (Linux)

    steps:
    - uses: actions/checkout@v3 # 1. 레포지토리 코드 가져오기
    
    - name: Set up JDK 17
      uses: actions/setup-java@v3 # 2. 자바 환경 설정
      with:
        java-version: '17'
        distribution: 'temurin'
        
    - name: Grant execute permission for gradlew
      run: chmod +x gradlew
      
    - name: Build with Gradle
      run: ./gradlew build # 3. 빌드 및 테스트 실행
```

---

## 3. GitHub Actions의 장점
1. **GitHub와 완벽 통합**: 별도의 CI/CD 서버(Jenkins 등)를 관리할 필요가 없습니다.
2. **Action 마켓플레이스**: 전 세계 사용자들이 만든 수많은 Action을 가져다 쓸 수 있습니다.
3. **무료 제공**: 퍼블릭 레포지토리는 완전 무료이며, 프라이빗 레포지토리도 일정 수준까지 무료입니다.

---

## 4. Git CI/CD의 흐름 (GitOps 맛보기)
1. 개발자가 기능을 개발하고 Git에 **Push**.
2. GitHub Actions가 이벤트를 감지하여 **자동 빌드 및 테스트** 실행.
3. 테스트 성공 시, Docker 이미지 빌드 후 저장소에 **Push**.
4. 운영 서버(혹은 Kubernetes)에 새로운 버전 **배포** 자동 수행.

---

## 5. 요약
Git CI/CD는 **"Git 이벤트를 트리거로 삼아 자동으로 빌드-테스트-배포하는 시스템"**이며, 현재 가장 대중적으로 사용되는 도구는 **GitHub Actions**입니다. 
