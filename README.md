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
- Terminal: VSCode Powershell
- Docker: 
- Git: version 2.56.0.windows.4
### 검사 명령어
```powershell
systeminfo
호스트 이름:             GNIA-C202-F01
OS 이름:                 Microsoft Windows 11 Pro
OS 버전:                 10.0.26300 N/A 빌드 26300
...이하생략

git --version
git version 2.56.0.windows.4
```

## 터미널 로그
### 기본 파일/디렉토리 조작
```powershell
# 현재 위치 확인
pwd 
Path             
----             
C:\work\Cdsy_E1-1

# 현재 위치 이동
C:\work\Cdsy_E1-1> cd testdir
C:\work\Cdsy_E1-1\testdir>

C:\work\Cdsy_E1-1\testdir> cd ..
C:\work\Cdsy_E1-1>

# 파일 목록 확인
Get-ChildItem -Force
    디렉터리: C:\work\Cdsy_E1-1
Mode                 LastWriteTime         Length Name
d--h--      2026-11-03  오전 11:11                .git
----                 -------------         ------ ----
-a----      2026-11-03  오전 11:41           1314 README.md

# 디렉터리 생성
mkdir testdir
    디렉터리: C:\work\Cdsy_E1-1
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----      2026-11-03  오후 12:05                testdir

# 새로운 빈 파일 생성
New-Item -ItemType File testdir/testfile.txt 
# linux 명령어인 touch가 작동하지 않아 windows용 명령어를 탐색, 실행
# 메모(최종삭제): 명령어에서 function과 alias 개념 학습 기록
    디렉터리: C:\work\Cdsy_E1-1\testdir
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----      2026-11-03  오후 12:34              0 testfile.txt

# 파일 이동
mv testdir/testfile.txt ./
(실행 결과 없음, testdir에 있던 파일을 현재 작업 디렉터리로 이동)

#파일 복사
cp testfile.txt testdir
(실행 결과 없음, testfile.txt의 사본을 testdir 내에 생성)

# 파일 이름 변경
mv testfile.txt test2.py
(실행 결과 없음, testfile.txt를 test2.py로 이름 변경)

# 이 시점에서 수동으로 test2.py에 내용 입력
# 파일 내용 조회
cat test2.py
print("Hello World")

# 파일 삭제
rm test2.py
(실행 결과 없음, test2.py 파일 삭제)

# 디렉터리 삭제
rmdir testdir -r
(실행 결과 없음, testdir 디렉터리 삭제)
```