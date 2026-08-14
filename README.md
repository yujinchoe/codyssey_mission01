# 개발 워크스테이션 구축 (Codyssey Mission 01)

## 1. 프로젝트 개요
본 프로젝트는 개발자로서의 첫 번째 기반인 '도구'를 다루는 역량을 갖추기 위해 진행되었습니다. 코드가 '내 컴퓨터에서만' 돌아가는 문제를 방지하고, 팀원 누구나 동일하게 실행 및 배포할 수 있는 재현 가능한 환경을 구성하는 것을 목표로 합니다. 이를 위해 **리눅스 CLI(터미널)**, **Docker(컨테이너)**, **Git/GitHub(버전 관리)** 를 직접 세팅하고 동작 원리를 검증하였습니다.

## 2. 실행 환경
* **OS:** macOS 15.7.4
* **Shell / Terminal:** zsh (/bin/zsh)
* **Docker 버전:** Docker version 28.5.2, build ecc6942 (OrbStack 기반)
* **Git 버전:** git version 2.53.0

## 3. 수행 항목 체크리스트
- [x] 터미널 기본 조작 및 폴더 구성 완료
- [x] 권한 변경 실습 (파일/디렉토리) 완료
- [x] Docker 설치 확인 완료 (`docker --version`)
- [ ] Docker 데몬 세부 점검 (`docker info`)
- [ ] Docker 기본 운영 명령 수행 및 hello-world 실행
- [ ] 컨테이너 실행(Ubuntu) 및 종료/유지(exit vs exec) 차이 확인
- [ ] Dockerfile 기반 커스텀 이미지 제작 및 컨테이너 실행
- [ ] 포트 매핑 접속(브라우저/curl) 성공 증거 확보
- [ ] 바인드 마운트 반영 검증
- [ ] Docker 볼륨 데이터 영속성 검증
- [ ] Git 설정 및 GitHub 연동

---

## 4. 검증 방법 및 수행 로그

### 4.1. 터미널 조작 및 권한 실습
* **수행 로그:**
```bash
# 디렉토리 생성 및 이동
$mkdir mission_practice$ cd mission_practice

# 파일 생성 및 확인
$ touch sample.txt
$ echo "Terminal practice" > sample.txt
$ ls -la
total 0
drwxr-xr-x  3 [username]  [username]    96  8 14 15:34 .
-rw-r--r--  1 [username]  [username]    18  8 14 15:34 sample.txt

# 복사, 이동, 삭제
$ cp sample.txt copy.txt
$ mv copy.txt rename.txt
$ rm rename.txt

# 권한 실습 (macOS 기본값이 644/755로 설정되어 있음을 확인)
$ chmod 644 sample.txt
$ ls -l sample.txt
-rw-r--r--  1 [username]  [username]  18  8 14 15:34 sample.txt

$ cd ..
$chmod 755 mission_practice$ ls -ld mission_practice
drwxr-xr-x  3 [username]  [username]  96  8 14 15:34 mission_practice

```

### 4.2. Docker 설치 및 점검

* **수행 로그:**

```bash
$ docker --version
Docker version 28.5.2, build ecc6942

$ docker info
# [다음 단계에서 수행 후 출력 결과 기입 예정]

```

### 4.3. Docker 기본 운영 및 컨테이너 실행

* **수행 로그:**

```bash
# [다음 단계에서 hello-world 및 ubuntu 실습 수행 후 로그 기입 예정]

```

---

## 5. 트러블슈팅

### [Case 1] Git Clone 시 Permission denied (publickey) 오류

* **문제:** SSH 방식으로 clone 시도 시 접근 거부.
* **해결:** HTTPS 방식을 사용하여 성공적으로 clone함.

### [Case 2] 터미널에서 docker 명령어 인식 불가

* **문제:** `zsh: command not found: docker` 에러 발생.
* **해결:** OrbStack 애플리케이션을 실행하여 Docker 엔진을 구동시킴.
