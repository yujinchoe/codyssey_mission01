# 개발 워크스테이션 구축 (Codyssey Mission 01)

## 1. 프로젝트 개요
본 프로젝트는 개발자로서의 첫 번째 기반인 '도구'를 다루는 역량을 갖추기 위해 진행되었습니다. 코드가 "내 컴퓨터에서만" 돌아가는 문제를 방지하고, 팀원 누구나 동일하게 실행 및 배포할 수 있는 재현 가능한 환경을 구성하는 것을 목표로 합니다.
서울캠퍼스 환경의 시스템 보안 정책 제약 속에서 **OrbStack**을 활용해 `sudo` 권한 없이 컨테이너 환경을 구축했으며, **리눅스 CLI(터미널)**, **Docker(컨테이너)**, **Git/GitHub(버전 관리 및 협업)**를 직접 세팅하고 동작 원리를 검증하였습니다.

## 2. 실행 환경
* **OS:** macOS 15.7.4
* **Shell / Terminal:** zsh (/bin/zsh)
* **Docker 버전:** Docker version 29.4.0 (OrbStack 기반)
* **Git 버전:** git version 2.53.0

## 3. 수행 항목 체크리스트
- [x] 터미널 기본 조작 및 폴더 구성 완료
- [x] 권한 변경 실습 (파일/디렉토리) 완료
- [x] Docker 설치 및 데몬 점검 완료 (`docker --version`, `docker info`)
- [x] Docker 기본 운영 명령 수행 및 `hello-world` 실행
- [x] 컨테이너 실행(Ubuntu) 및 종료/유지 (`exit` vs `exec`) 차이 확인
- [x] Dockerfile 기반 커스텀 이미지 제작 및 컨테이너 실행 (NGINX 베이스)
- [x] 포트 매핑 접속(`curl` 및 브라우저 포트 포워딩) 성공 증거 확보
- [x] Docker 볼륨 데이터 영속성 검증 완료
- [x] Git 설정 및 GitHub 연동 완료

---

## 4. 검증 방법 및 수행 로그

### 4.1. 터미널 조작 및 권한 실습
```bash
# 디렉토리 생성 및 이동
$mkdir mission_practice$ cd mission_practice

# 파일 생성 및 확인
$ touch sample.txt
$ echo "Terminal practice" > sample.txt
$ ls -la
drwxr-xr-x  3 user  user   96  8 14 15:34 .
-rw-r--r--  1 user  user   18  8 14 15:34 sample.txt

# 권한 실습 및 확인
$ chmod 644 sample.txt
$ ls -l sample.txt
-rw-r--r--  1 user  user  18  8 14 15:34 sample.txt

$ cd ..
$chmod 755 mission_practice$ ls -ld mission_practice
drwxr-xr-x  3 user  user  96  8 14 15:34 mission_practice

```

### 4.2. Docker 설치 및 점검

```bash
$ docker --version
Docker version 29.4.0, build 9d7ad9f

$ docker info
# (OrbStack 기반 Docker 데몬 및 컨테이너드 런타임 정상 동작 확인 완료)

```

### 4.3. Docker 기본 운영 및 컨테이너 실행

```bash
# hello-world 실행
$ docker run hello-world

# ubuntu 컨테이너 실습 (exit vs exec 비교)
$ docker run -it --name my-ubuntu ubuntu bash
root@bc5f5a95cf09:/# exit  # 컨테이너 종료 (Exited 상태)

$docker run -d --name keep-ubuntu ubuntu sleep infinity$ docker exec -it keep-ubuntu bash
root@2834602bb6dd:/# exit  # 접속만 해제, 컨테이너 유지 (Up 상태)

$ docker rm -f my-ubuntu keep-ubuntu

```

### 4.4. 커스텀 이미지 제작 및 포트 매핑 접속

* **Dockerfile 내용:**

```dockerfile
FROM nginx:alpine
COPY app/index.html /usr/share/nginx/html/index.html

```

* **수행 로그:**

```bash
$ docker build -t my-custom-web:1.0 .
$docker run -d -p 8080:80 --name web-8080 my-custom-web:1.0$ curl http://localhost:8080
<h1>Hello from Codyssey Custom Web Server!</h1>

```

### 4.5. Docker 볼륨 영속성 검증

```bash
# 볼륨 생성 및 첫 번째 컨테이너에 연결 후 데이터 기록
$ docker volume create my_data_vol
$docker run -d --name vol-test1 -v my_data_vol:/data ubuntu sleep infinity$ docker exec -it vol-test1 bash -lc "echo 'Codyssey volume test' > /data/test.txt"

# 컨테이너 강제 삭제
$ docker rm -f vol-test1

# 새로운 컨테이너 생성하여 동일 볼륨 연결 후 데이터 확인
$docker run -d --name vol-test2 -v my_data_vol:/data ubuntu sleep infinity$ docker exec -it vol-test2 cat /data/test.txt
Codyssey volume test

# 정리
$docker rm -f vol-test2$ docker volume rm my_data_vol

```

---

## 5. 트러블슈팅

### [Case 1] 터미널에서 zsh 특수문자 입력 에러 (`zsh: event not found`)

* **문제 상황:** `echo "<h1>..."` 명령 실행 시 zsh 셸에서 태그 형태의 문자를 히스토리 확장 기호로 오해하여 에러 발생.
* **원인 가설:** 셸 파서가 특정 특수문자를 인라인 명령어로 해석하려고 시도함.
* **해결 방법:** `cat << 'EOF'` 다중 행 입력 구문이나 작은따옴표를 사용하여 특수문자 간섭 없이 안전하게 파일을 생성함.

### [Case 2] Git Clone 및 초기 커밋 인증 문제

* **문제 상황:** 초기 커밋 시 사용자 정보 미설정 경고 및 원격 저장소 푸시 시 인증 거부 발생.
* **해결 방법:** `git config --global user.name` 및 `user.email`을 올바르게 등록하고, HTTPS 인증 시 GitHub Personal Access Token(PAT)을 발급받아 적용함.