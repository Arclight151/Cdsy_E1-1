# 코디세이 1-1 : 개발 워크스테이션 구축

## 미션 목표 요약
- 터미널(CLI)로 디렉토리/파일/권한을 다뤄본다.
- Docker를 설치·점검하고 컨테이너 실행/관리 흐름을 익힌다.
- Dockerfile로 커스텀 이미지를 만들고 포트 매핑, 바인드 마운트, 볼륨 영속성을 검증한다.
- Git/GitHub 연동을 통해 결과를 저장소에 정리한다.

## 수행 항목
- [ ] 터미널 기본 조작
- [ ] 파일/디렉토리 권한 실습
- [ ] Docker 설치 및 점검
- [ ] hello-world / ubuntu 컨테이너 실행
- [ ] Dockerfile 기반 커스텀 이미지 제작
- [ ] 포트 매핑 검증
- [ ] 바인드 마운트 검증
- [ ] Docker 볼륨 영속성 검증
- [ ] Git/GitHub 연동
- [ ] 트러블슈팅 정리

## 실행 환경
- OS: Windows 11
- Terminal: VSCode Powershell 및 Git Bash
- Docker: version 29.9.2, build a82e528
- Git: version 2.56.0.windows.4
### 검사 명령어
```powershell
> systeminfo
호스트 이름:             GNIA-C202-F01
OS 이름:                 Microsoft Windows 11 Pro
OS 버전:                 10.0.26300 N/A 빌드 26300
...이하생략

> git --version
git version 2.56.0.windows.4

> docker --version
Docker version 29.9.2, build a82e528
```

## 터미널 로그
### 기본 파일/디렉토리 조작
```powershell
# powershell에서 수행
# 현재 위치 확인
> pwd 
Path             
----             
C:\work\Cdsy_E1-1

# 현재 위치 이동
C:\work\Cdsy_E1-1> cd testdir
C:\work\Cdsy_E1-1\testdir>

C:\work\Cdsy_E1-1\testdir> cd ..
C:\work\Cdsy_E1-1>

# 파일 목록 확인
> Get-ChildItem -Force
    디렉터리: C:\work\Cdsy_E1-1
Mode                 LastWriteTime         Length Name
d--h--      2026-11-03  오전 11:11                .git
----                 -------------         ------ ----
-a----      2026-11-03  오전 11:41           1314 README.md

# 디렉터리 생성
> mkdir testdir
    디렉터리: C:\work\Cdsy_E1-1
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----      2026-11-03  오후 12:05                testdir

# 새로운 빈 파일 생성
> New-Item -ItemType File testdir/testfile.txt 
# linux 명령어인 touch가 작동하지 않아 windows용 명령어를 탐색, 실행
# 메모(최종삭제): 명령어에서 function과 alias 개념 학습 기록
    디렉터리: C:\work\Cdsy_E1-1\testdir
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----      2026-11-03  오후 12:34              0 testfile.txt

# 파일 이동
> mv testdir/testfile.txt ./
> ls testfile.txt
testfile.txt

#파일 복사
> cp testfile.txt testdir
(실행 결과 없음, testfile.txt의 사본을 testdir 내에 생성)

# 파일 이름 변경
> mv testfile.txt test2.py
(실행 결과 없음, testfile.txt를 test2.py로 이름 변경)

# 이 시점에서 수동으로 test2.py에 내용 입력
# 파일 내용 조회
> cat test2.py
print("Hello World")

# 파일 삭제
> rm test2.py
> ls test2.py
ls: cannot access 'test2.py': No such file or directory

# 디렉터리 삭제
> rmdir testdir -r
> ls testdir
ls: cannot access 'testdir': No such file or directory
```

### 권한 설정 실습
```bash
# git bash에서 수행
# 파일 권한 확인
> ls -l testfile.py 
-rw-r--r-- 1 david0205110511 1049089 0 11월  3 13:42 testfile.py
# r=read, w=write, x=execute 권한

# 권한 변경
> chmod -v 777 testfile.py # (-v: verbose, 처리 결과 표시)
mode of 'testfile.py' changed from 0644 (rw-r--r--) to 0777 (rwxrwxrwx)

> ls -lsa testfile.py
1 -rw-r--r-- 1 david0205110511 1049089 20 11월  3 13:47 testfile.py
```
*Troubleshooting 1: 검증해봤을 때 권한이 변경되지 않은 것 같아 bash가 아닌 명령 프롬프트에서 재시도
```cmd
# cmd
# 파일 권한 확인
> icacls .\testfile.py
.\testfile.py BUILTIN\Administrators:(I)(F)
              NT AUTHORITY\SYSTEM:(I)(F)
              BUILTIN\Users:(I)(RX)
              NT AUTHORITY\Authenticated Users:(I)(M)

# 권한 변경
> icacls testfile.py /deny %USERNAME%:R
처리된 파일: testfile.py
1 파일을 처리했으며 0 파일은 처리하지 못했습니다.

# 변경 후 결과: file_read_denied.png 참조
```
![img1](./imgs/file_read_denied.png)
```cmd
# 작업 후 원상복구
> icacls testfile.py /reset
```
```cmd
# cmd
# 디렉터리 권한 확인
> icacls testdir
testdir BUILTIN\Administrators:(I)(OI)(CI)(F)
        NT AUTHORITY\SYSTEM:(I)(OI)(CI)(F)
        BUILTIN\Users:(I)(OI)(CI)(RX)
        NT AUTHORITY\Authenticated Users:(I)(M)
        NT AUTHORITY\Authenticated Users:(I)(OI)(CI)(IO)(M)

> icacls testdir /deny %USERNAME%:R
처리된 파일: testdir
1 파일을 처리했으며 0 파일은 처리하지 못했습니다.

# 변경 후 결과: dir_read_denied.png 참조
```
![img2](./imgs/dir_read_denied.png)
```cmd
# 작업 후 원상복구
> icacls testdir /reset
```

## Docker 실습
### Docker 설치 점검
```powershell
> docker --version
Docker version 29.9.2, build a82e528

> docker info
# *logs/Docker_info_log 참조, 클라이언트 및 서버 정상 구동 중
```

### Docker 기본 명령 수행
```powershell
# hello-world 샘플 실행
> docker run hello-world
# *logs/Docker_helloworld_log 참조

# 다운로드된 이미지 확인
> docker images
i Info →   U  In Use
IMAGE                ID             DISK USAGE   CONTENT SIZE   EXTRA
hello-world:latest   c567b231f656       25.9kB         9.49kB    U

# 컨테이너 정보 확인
> docker ps -a
CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                     PORTS     NAMES
154c82a1e128   hello-world   "/hello"   23 minutes ago   Exited (0) 5 minutes ago             gracious_shockley

# 컨테이너 로그 확인
> docker logs --tail 5 --timestamps gracious_shockley
2026-11-04T07:05:03.489942513Z  https://hub.docker.com/
2026-11-04T07:05:03.489942929Z
2026-11-04T07:05:03.489943307Z For more examples and ideas, visit:
2026-11-04T07:05:03.489943799Z  https://docs.docker.com/get-started/
2026-11-04T07:05:03.489944160Z
```

### ubuntu 컨테이너 실습
```powershell
# ubuntu 컨테이너 다운로드, 실행
> docker run -it ubuntu bash
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
f3f25b84a7d7: Pull complete
edafa32f5d9e: Pull complete
21594acc2d8b: Download complete
Digest: sha256:6a06111c3b0a8600feadb270aec771dc239bf407b25625f93f44ab00e11f4d3c
Status: Downloaded newer image for ubuntu:latest
root@d4531f023437:/#

# 컨테이너 내
root@d4531f023437:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@d4531f023437:/# pwd
/
root@d4531f023437:/# whoami
root

# powershell
> docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED         STATUS         PORTS     NAMES
d4531f023437   ubuntu    "bash"    4 minutes ago   Up 4 minutes             keen_torvalds
```

#### attach/exec
```powershell
# attach의 기능 확인
> docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS          PORTS     NAMES
6fb9ad2f317b   ubuntu    "bash"    3 seconds ago    Up 2 seconds              ubuntu-test-2
> docker attach ubuntu-test-2
```
```bash
root@6fb9ad2f317b:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@6fb9ad2f317b:/# pwd
/
root@6fb9ad2f317b:/# echo "attach test"
attach test
root@6fb9ad2f317b:/# exit
exit
```
```powershell
> docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS                      PORTS     NAMES
6fb9ad2f317b   ubuntu    "bash"    2 minutes ago    Exited (0) 19 seconds ago             ubuntu-test-2
```
```powershell
# exec의 기능 확인
> docker start ubuntu-test-2
ubuntu-test-2
> docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS          PORTS     NAMES
6fb9ad2f317b   ubuntu    "bash"    11 minutes ago   Up 8 seconds              ubuntu-test-2
> docker exec -it ubuntu-test-2 bash
```
```bash
root@6fb9ad2f317b:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@6fb9ad2f317b:/# pwd
/
root@6fb9ad2f317b:/# echo "exec test"
exec test
root@6fb9ad2f317b:/# exit
exit
```
```powershell
> docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS          PORTS     NAMES
6fb9ad2f317b   ubuntu    "bash"    17 minutes ago   Up 5 minutes              ubuntu-test-2
```

### Dockerfile 기반 커스텀 이미지 제작 및 포트 매핑
```powershell
# 소스 코드 및 Dockerfile은 /webapp 참조
> docker build -t codyw1-nginx:v1 ./webapp
[+] Building 6.3s (5/5) FINISHED
(이미지 빌드 로그)

> docker images
IMAGE             ID             DISK USAGE   CONTENT SIZE   EXTRA
codyw1-nginx:v1   d71e7ffd5688        241MB         63.9MB

> docker run -d --name w1-web-test -p 8080:80 codyw1-nginx:v1
> docker ps
CONTAINER ID   IMAGE             COMMAND                   CREATED          STATUS          PORTS                                     NAMES
b4bc8cb60ac1   codyw1-nginx:v1   "/docker-entrypoint.…"   13 seconds ago   Up 13 seconds   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   w1-web-test

> docker logs --tail 3 w1-web-test
2026/11/11 07:48:43 [notice] 1#1: start worker process 51
2026/11/11 07:48:43 [notice] 1#1: start worker process 52
172.17.0.1 - - [11/Nov/2026:07:48:54 +0000] "GET / HTTP/1.1" 200 199 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/157.0.0.0 Safari/537.36" "-"

> curl http://localhost:8080
StatusCode        : 200
StatusDescription : OK
Content           : <!DOCTYPE html>
                    <html lang="ko">
                    <head>
                      <meta charset="UTF-8">
                      <title>Codyssey W1</title>
                    </head>
                    <body>
                      <h1>Docker ì»¤ì¤í ì¹ ìë² íì¤í¸</h1>
                      <p>by djk</p>
                    </body>
                    </html>
RawContent        : HTTP/1.1 200 OK
                    Connection: keep-alive
                    Accept-Ranges: bytes
                    Content-Length: 199
                    Content-Type: text/html
                    Date: Wed, 11 Nov 2026 08:07:28 GMT
                    ETag: "6af41a45-c7"
                    Last-Modified: Wed, 11 Nov 2026 0...
Forms             : {}
Headers           : {[Connection, keep-alive], [Accept-Ranges, bytes], [Content-Length, 199], [Content-Type, text/html]
                    ...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : System.__ComObject
RawContentLength  : 199

# 웹페이지 이미지 하단 첨부
```
![img3](./imgs/webpage_loaded_1.png)

### 바인드 마운트
```powershell
> docker run -d --name w1-bindmount -p 8091:80 -v ${PWD}/src:/usr/share/nginx/html nginx:latest
> docker ps
CONTAINER ID   IMAGE          COMMAND                   CREATED          STATUS          PORTS                                     NAMES
daaa7e1e530e   nginx:latest   "/docker-entrypoint.…"   11 seconds ago   Up 11 seconds   0.0.0.0:8091->80/tcp, [::]:8091->80/tcp   w1-bindmount

# 바인드 마운트 성공 예제 첨부
```
![img4](./imgs/webpage_bindmount.png)

### 볼륨 영속성 검증
```powershell
> docker volume create w1-vol 
w1-vol

> docker volume ls
DRIVER    VOLUME NAME
local     w1-vol

> docker run -dit --name volume-test-1 -v w1-vol:/data alpine sh
> docker exec volume-test-1 sh -c "echo hello-volume > /data/test.txt"
> docker exec volume-test-1 cat /data/test.txt
hello-volume #cat 결과로 입력했던 텍스트가 성공적으로 반환
# 테스트 컨테이너 1 삭제
> docker rm -f volume-test-1

# 동일한 볼륨으로 다른 컨테이너 생성
> docker run -dit --name volume-test-2 -v w1-vol:/data alpine sh
> docker exec volume-test-2 cat /data/test.txt
hello-volume #컨테이너를 삭제했으나 볼륨에 남아있던 텍스트 데이터는 유지, 반환

> docker volume inspect w1-vol 
[
    {
        "CreatedAt": "2026-11-11T08:33:42Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/w1-vol/_data",
        "Name": "w1-vol",
        "Options": null,
        "Scope": "local"
    }
]
```
