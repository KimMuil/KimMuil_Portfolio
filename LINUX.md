# LINUX 학습노트

---

## 1. Linux 파일 시스템 구조 (FHS)

###  FHS (Filesystem Hierarchy Standard)란?

FHS는 Linux 및 Unix 계열 운영체제에서 파일과 디렉토리의 구조를 표준화한 규격입니다.  
모든 Linux 배포판은 이 표준에 따라 디렉토리들이 일관된 의미와 역할을 가집니다.

---

###  주요 디렉토리 구조 설명

| 디렉토리 | 설명 |
|----------|------|
| `/`      | 루트 디렉토리. 모든 디렉토리의 최상위 경로입니다. |
| `/bin`   | 필수 실행 파일(binary) 저장. 시스템 부팅과 직접 관련 있음. |
| `/boot`  | 부팅에 필요한 커널, grub 설정 파일 저장. |
| `/dev`   | 장치 파일(Device files) 저장. 예: sda, tty 등 |
| `/etc`   | 설정 파일(configuration) 저장. |
| `/home`  | 사용자 계정의 홈 디렉토리 위치. |
| `/lib`   | 커널 및 기본 명령어에 필요한 라이브러리 저장. |
| `/media` | 외부 저장 장치(USB 등) 자동 마운트 위치. |
| `/mnt`   | 수동 마운트용 임시 디렉토리. |
| `/opt`   | 사용자가 추가로 설치한 소프트웨어 저장. |
| `/proc`  | 커널과 프로세스 정보가 담긴 가상 파일 시스템. |
| `/root`  | root 계정의 홈 디렉토리. 일반 사용자와 구분. |
| `/run`   | 시스템 부팅 시 임시로 필요한 런타임 파일 저장. |
| `/sbin`  | 시스템 관리용 실행 파일 저장. 일반 사용자는 접근 제한. |
| `/tmp`   | 임시 파일 저장소. 재부팅 시 자동 삭제. |
| `/usr`   | 사용자 관련 프로그램 및 라이브러리 저장. `/usr/bin`, `/usr/lib` 등 |
| `/var`   | 로그, 메일, 큐 등 변화가 자주 생기는 데이터 저장.

---

###  참고 명령어 예시

```bash
# 최상위 디렉토리 확인
ls /

# /etc 디렉토리 내부 구조 확인
ls -l /etc

# /proc 디렉토리는 실시간 시스템 정보를 담고 있음
cat /proc/cpuinfo
cat /proc/meminfo
```

---

## 2. 기본 명령어 정리 (ls, cd, cp 등)

### 개요

Linux는 터미널을 기반으로 하는 시스템이기 때문에 기본 명령어 숙지는 필수입니다.  
아래는 자주 사용하는 핵심 명령어들을 간략한 예시와 함께 정리한 것입니다.

---

### 파일 및 디렉토리 관련 명령어

| 명령어 | 설명 | 예시 |
|--------|------|------|
| `ls`   | 현재 디렉토리의 파일/폴더 목록 출력 | `ls -l`, `ls -a` |
| `cd`   | 디렉토리 이동 | `cd /etc`, `cd ~`, `cd ..` |
| `pwd`  | 현재 작업 디렉토리 경로 출력 | `pwd` |
| `cp`   | 파일/디렉토리 복사 | `cp a.txt b.txt`, `cp -r dir1 dir2` |
| `mv`   | 파일/디렉토리 이동 또는 이름 변경 | `mv old.txt new.txt` |
| `rm`   | 파일/디렉토리 삭제 | `rm file.txt`, `rm -r folder` |
| `mkdir`| 디렉토리 생성 | `mkdir mydir` |
| `rmdir`| 빈 디렉토리 삭제 | `rmdir emptydir` |

---

### 보기 및 검색 관련 명령어

| 명령어 | 설명 | 예시 |
|--------|------|------|
| `cat`    | 파일 내용 출력 | `cat file.txt` |
| `more`   | 파일을 한 페이지씩 보기 | `more file.txt` |
| `less`   | `more`보다 유연한 보기 명령어 | `less file.txt` |
| `head`   | 앞부분 10줄 보기 | `head file.txt`, `head -n 5 file.txt` |
| `tail`   | 마지막 10줄 보기 | `tail file.txt`, `tail -f log.txt` |
| `find`   | 파일/디렉토리 검색 | `find / -name "test.txt"` |
| `grep`   | 파일 내 특정 문자열 검색 | `grep "root" /etc/passwd` |

---

### 파일 정보 및 디스크 확인

| 명령어 | 설명 | 예시 |
|--------|------|------|
| `df`   | 디스크 사용량 확인 | `df -h` |
| `du`   | 디렉토리/파일의 용량 확인 | `du -sh *` |
| `stat` | 파일 상세 정보 출력 | `stat file.txt` |

---

### 실습 예시

```bash
# 홈 디렉토리로 이동 후 숨긴 파일까지 모두 보기
cd ~
ls -al

# /var/log 디렉토리의 크기 확인
du -sh /var/log

# 현재 시스템의 디스크 사용률 확인
df -h
```

---

## 3. 파일 및 디렉토리 관리

### 개요

Linux에서는 모든 데이터가 파일로 취급됩니다. 파일과 디렉토리를 생성, 복사, 이동, 삭제하는 기본적인 관리 능력은 시스템 운용의 기초입니다.

---

## 파일 생성 및 관리 명령어

| 명령어 | 설명 | 예시 |
|--------|------|------|
| touch | 새 파일 생성 | touch file.txt |
| cp | 파일 복사 | cp a.txt b.txt, cp a.txt /tmp/ |
| mv | 파일 이동 또는 이름 변경 | mv a.txt b.txt, mv a.txt /tmp/ |
| rm | 파일 삭제 | rm a.txt |
| cat | 파일 내용 보기 | cat a.txt |
| nano, vi | 텍스트 편집기 실행 | nano file.txt, vi file.txt |

---

## 디렉토리 관리 명령어

| 명령어 | 설명 | 예시 |
|--------|------|------|
| mkdir | 새 디렉토리 생성 | mkdir newdir |
| mkdir -p | 하위 디렉토리까지 생성 | mkdir -p dir1/dir2/dir3 |
| rmdir | 빈 디렉토리 삭제 | rmdir emptydir |
| rm -r | 디렉토리 및 내용 삭제 | rm -r dir1 |
| ls -l | 디렉토리 내용 상세 보기 | ls -l /home |

---

## 실습 예시

```bash
# 'project' 디렉토리를 만들고 그 안에 파일을 하나 생성
mkdir project
touch project/README.md

# 디렉토리 전체 복사
cp -r project backup_project

# 디렉토리 삭제
rm -r backup_project
```

---

## 4. 사용자 및 그룹 관리

### 개요

Linux는 다중 사용자 운영체제로, 사용자 계정과 그룹을 생성하고 관리하는 기능이 중요합니다.  
적절한 사용자 및 그룹 설정은 보안과 시스템 안정성 유지에 필수적입니다.

---

## 사용자 관리 명령어

| 명령어 | 설명 | 예시 |
|--------|------|------|
| adduser | 새 사용자 계정 생성 (권장) | adduser alice |
| useradd | 새 사용자 계정 생성 (기본) | useradd bob |
| passwd | 사용자 비밀번호 설정 또는 변경 | passwd alice |
| userdel | 사용자 계정 삭제 | userdel bob |
| id | 사용자 ID 및 그룹 정보 확인 | id alice |
| whoami | 현재 로그인된 사용자 확인 | whoami |
| su | 다른 사용자로 전환 | su - root |
| sudo | 관리자 권한으로 명령 실행 | sudo apt update |

---

## 그룹 관리 명령어

| 명령어 | 설명 | 예시 |
|--------|------|------|
| groupadd | 새 그룹 생성 | groupadd developers |
| groupdel | 그룹 삭제 | groupdel testgroup |
| usermod -aG | 사용자에게 그룹 추가 | usermod -aG developers alice |
| groups | 사용자가 속한 그룹 보기 | groups alice |

---

## 실습 예시

```bash
# 'devuser'라는 사용자 계정 생성 후 비밀번호 설정
adduser devuser
passwd devuser

# 'devgroup'이라는 그룹 생성하고 사용자 추가
groupadd devgroup
usermod -aG devgroup devuser

# 사용자 계정 삭제
userdel devuser
```

---

## 5. 권한 및 퍼미션 설정 (chmod, chown 등)

### 개요

Linux에서는 파일과 디렉토리에 대해 사용자 권한을 설정할 수 있습니다.  
권한은 보안 및 파일 공유 제어에 매우 중요한 요소로, 소유자, 그룹, 기타 사용자에게 각각 다르게 설정할 수 있습니다.

---

## 권한 구조

권한은 **소유자(owner)**, **그룹(group)**, **기타 사용자(other)** 로 나뉘며, 각각에 대해 다음 권한을 부여할 수 있습니다:

- r (read): 읽기
- w (write): 쓰기
- x (execute): 실행

예: `-rwxr-xr--`  
| 대상 | 권한 |
|------|------|
| 사용자 | rwx (읽기, 쓰기, 실행) |
| 그룹 | r-x (읽기, 실행) |
| 기타 사용자 | r-- (읽기만) |

---

## 권한 관련 명령어

| 명령어 | 설명 | 예시 |
|--------|------|------|
| chmod | 파일/디렉토리 권한 변경 | chmod 755 file.txt, chmod u+x run.sh |
| chown | 파일 소유자 변경 | chown user1 file.txt |
| chgrp | 파일 소유 그룹 변경 | chgrp devs file.txt |
| umask | 기본 권한 설정 값 확인 및 설정 | umask, umask 022 |

---

## 숫자 권한 표기법

| 숫자 | 권한 | 의미 |
|------|--------|-------------------|
| 7 | rwx | 읽기 + 쓰기 + 실행 |
| 6 | rw- | 읽기 + 쓰기 |
| 5 | r-x | 읽기 + 실행 |
| 4 | r-- | 읽기 |
| 3 | -wx | 쓰기 + 실행 |
| 2 | -w- | 쓰기 |
| 1 | --x | 실행 |
| 0 | --- | 없음 |

---

## 실습 예시

```bash
# 파일을 모든 사용자에게 읽기 가능하게 설정
chmod 644 report.txt

# 스크립트 파일에 실행 권한 추가
chmod +x install.sh

# 파일의 소유자를 user1으로 변경
chown user1 data.csv

# 파일의 그룹을 devs로 변경
chgrp devs data.csv
```

---

## 6. 프로세스 관리 (ps, top, kill 등)

### 개요

Linux에서는 실행 중인 프로그램을 '프로세스'라고 하며, 이를 모니터링하고 제어하는 기능이 중요합니다.  
프로세스의 상태를 확인하거나, 필요에 따라 종료하는 등의 작업은 시스템 운영의 핵심입니다.

---

## 주요 프로세스 명령어

| 명령어 | 설명 | 예시 |
|--------|------|------|
| ps | 현재 실행 중인 프로세스 목록 출력 | ps, ps aux, ps -ef |
| top | 실시간 시스템 리소스 및 프로세스 상태 확인 | top |
| htop | top의 대체 인터페이스 (패키지 설치 필요) | htop |
| kill | 특정 PID를 가진 프로세스 종료 | kill 1234 |
| killall | 이름으로 프로세스 전체 종료 | killall firefox |
| nice | 프로세스 시작 시 우선순위 설정 | nice -n 10 ./script.sh |
| renice | 실행 중인 프로세스 우선순위 변경 | renice -n 5 1234 |
| jobs | 백그라운드 작업 확인 | jobs |
| fg | 백그라운드 작업을 포그라운드로 이동 | fg %1 |
| bg | 정지된 작업을 백그라운드로 실행 | bg %1 |

---

## 프로세스 상태 약어 (ps 출력 시)

| 상태 | 의미 |
|------|------|
| R | 실행 중 (Running) |
| S | 절전 상태 (Sleeping) |
| D | 대기 상태 (Uninterruptible sleep) |
| T | 일시 중지 (Stopped) |
| Z | 좀비 프로세스 (Zombie) |

---

## 실습 예시

```bash
# 현재 사용자의 프로세스 확인
ps

# 전체 프로세스 상세 보기
ps aux

# 가장 많은 리소스를 사용하는 프로세스 종료
top   # 상단에 표시된 PID 확인
kill [PID]

# 이름으로 프로세스 종료
killall code

# 특정 스크립트를 낮은 우선순위로 실행
nice -n 10 ./myscript.sh
```

---

## 7. 패키지 관리 (apt, yum, rpm 등)

### 개요

Linux에서는 필요한 소프트웨어를 패키지 형태로 관리하며, 배포판에 따라 패키지 관리 도구가 다릅니다.  
대표적으로 Debian 계열은 `apt`, RedHat 계열은 `yum` 또는 `dnf`, 패키지 자체는 `.deb` 또는 `.rpm` 확장자를 사용합니다.

---

## Debian 계열 (Ubuntu, Debian 등)

| 명령어 | 설명 | 예시 |
|--------|------|------|
| apt update | 패키지 목록 갱신 | sudo apt update |
| apt upgrade | 설치된 패키지 업그레이드 | sudo apt upgrade |
| apt install | 패키지 설치 | sudo apt install vim |
| apt remove | 패키지 제거 | sudo apt remove vim |
| apt purge | 설정 파일까지 제거 | sudo apt purge vim |
| apt autoremove | 불필요한 패키지 자동 제거 | sudo apt autoremove |
| dpkg -i | .deb 패키지 설치 | sudo dpkg -i sample.deb |

---

## RedHat 계열 (CentOS, RHEL, Fedora 등)

| 명령어 | 설명 | 예시 |
|--------|------|------|
| yum install | 패키지 설치 | sudo yum install httpd |
| yum remove | 패키지 제거 | sudo yum remove httpd |
| yum update | 전체 패키지 업데이트 | sudo yum update |
| rpm -ivh | .rpm 패키지 설치 | sudo rpm -ivh package.rpm |
| rpm -e | .rpm 패키지 제거 | sudo rpm -e package-name |

---

## 실습 예시

```bash
# Ubuntu에서 Git 설치
sudo apt update
sudo apt install git

# CentOS에서 Apache 설치
sudo yum install httpd

# .deb 파일 수동 설치
sudo dpkg -i custom-tool.deb

```

---

## 8. 네트워크 설정 및 명령어

### 개요

Linux 시스템의 네트워크 상태를 확인하고 설정하는 명령어는 서버 운용과 문제 해결에 필수적입니다.  
IP 설정, 연결 확인, 포트 상태 확인 등을 위해 다양한 명령어가 사용됩니다.

---

## 주요 네트워크 명령어

| 명령어 | 설명 | 예시 |
|--------|------|------|
| ip addr | IP 주소 및 인터페이스 확인 | ip addr, ip a |
| ip link | 네트워크 장치 상태 확인 및 설정 | ip link show, ip link set eth0 up |
| ifconfig | 네트워크 정보 확인 (구버전) | ifconfig |
| ping | 호스트와의 연결 상태 확인 | ping 8.8.8.8 |
| traceroute | 패킷 경로 추적 | traceroute google.com |
| netstat | 네트워크 포트, 연결 상태 확인 | netstat -tulnp |
| ss | netstat 대체 명령어 | ss -tuln |
| nslookup | DNS 서버로부터 IP 주소 조회 | nslookup naver.com |
| dig | DNS 정보 조회 (자세한 출력) | dig google.com |
| hostname | 현재 시스템의 호스트네임 확인 및 설정 | hostname, hostnamectl set-hostname server1 |

---

## IP 주소 수동 설정 (임시)

```bash
# eth0에 192.168.1.100/24 할당
sudo ip addr add 192.168.1.100/24 dev eth0

# eth0 인터페이스 활성화
sudo ip link set eth0 up
```

## 네트워크 재시작 (Ubuntu 예시)

```bash
# NetworkManager를 사용하는 시스템
sudo systemctl restart NetworkManager

# 또는 전통적인 networking 서비스 사용 시
sudo service networking restart
```

## 실습 예시

```bash
# 1. 자신의 IP 주소 확인 (간단하게 보기)
ip a

# 2. 네트워크 인터페이스에 IP 주소 수동 설정
sudo ip addr add 192.168.10.100/24 dev eth0
sudo ip link set eth0 up

# 3. 구글에 연결이 가능한지 테스트
ping -c 4 google.com

# 4. DNS 서버 작동 여부 확인
nslookup google.com
dig naver.com

# 5. 현재 열려 있는 포트 확인
sudo ss -tuln

# 6. 네트워크 서비스 재시작 (Ubuntu 기준)
sudo systemctl restart NetworkManager
```
---

## 9. 서비스 및 데몬 관리 (systemctl)

### 개요

Linux 시스템은 다양한 **서비스(데몬)**를 자동으로 실행하거나 수동으로 제어할 수 있습니다.  
`systemctl`은 systemd 기반의 시스템에서 서비스 상태를 제어하는 표준 명령어입니다.

---

## systemctl 명령어 기본

| 명령어 | 설명 | 예시 |
|--------|------|------|
| systemctl status | 서비스 상태 확인 | systemctl status ssh |
| systemctl start | 서비스 시작 | sudo systemctl start apache2 |
| systemctl stop | 서비스 중지 | sudo systemctl stop apache2 |
| systemctl restart | 서비스 재시작 | sudo systemctl restart apache2 |
| systemctl enable | 부팅 시 자동 시작 등록 | sudo systemctl enable apache2 |
| systemctl disable | 부팅 시 자동 시작 해제 | sudo systemctl disable apache2 |
| systemctl is-enabled | 자동 시작 여부 확인 | systemctl is-enabled apache2 |
| systemctl list-units --type=service | 실행 중인 서비스 목록 확인 | systemctl list-units --type=service |

---

## 실습 예시

```bash
# 1. SSH 서비스 상태 확인
systemctl status ssh

# 2. Apache2 웹 서버 실행 및 자동 실행 등록
sudo systemctl start apache2
sudo systemctl enable apache2

# 3. 서비스 중지 및 재시작
sudo systemctl stop apache2
sudo systemctl restart apache2

# 4. 실행 중인 서비스 목록 확인
systemctl list-units --type=service
```

---

## 10. 로그 관리 (/var/log, journalctl 등)

### 개요

Linux 시스템은 운영 중 발생하는 다양한 이벤트를 **로그 파일**로 저장합니다.  
이 로그는 문제 해결, 보안 감사, 시스템 상태 파악 등에 매우 중요합니다.

---

## 주요 로그 파일 위치 (/var/log)

| 로그 파일 | 설명 |
|-----------|------|
| /var/log/syslog | 시스템 전반 로그 (Ubuntu 등) |
| /var/log/messages | 일반 시스템 로그 (CentOS 등) |
| /var/log/auth.log | 인증 및 로그인 관련 로그 |
| /var/log/dmesg | 부팅 시 커널 메시지 |
| /var/log/kern.log | 커널 관련 로그 |
| /var/log/boot.log | 부팅 관련 로그 |
| /var/log/cron | cron 작업 관련 로그 |
| /var/log/secure | 보안 관련 인증 로그 (RHEL 계열) |
| /var/log/httpd/ | Apache 웹 서버 로그 |
| /var/log/nginx/ | Nginx 웹 서버 로그 |

---

## 명령어 정리

| 명령어 | 설명 | 예시 |
|--------|------|------|
| cat / tail / less | 로그 파일 내용 확인 | tail -f /var/log/syslog |
| grep | 특정 키워드 검색 | grep "error" /var/log/syslog |
| journalctl | systemd 로그 확인 | journalctl -xe |
| dmesg | 커널 부팅 메시지 확인 | dmesg | less |
| logrotate | 로그 자동 순환 설정 | /etc/logrotate.conf 참고 |

---

## 실습 예시

```bash
# 실시간으로 시스템 로그 확인
tail -f /var/log/syslog

# 인증 실패 로그 확인
grep "Failed" /var/log/auth.log

# 커널 메시지 확인
dmesg | less

# systemd 서비스 오류 확인
journalctl -xe

# Apache 웹 서버 접근 로그 확인
cat /var/log/apache2/access.log
```

