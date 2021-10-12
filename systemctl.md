# systemctl 명령어

Systemd(system daemon)은 전통적으로 Unix 시스템이 부팅후에 가장 먼저 생성된 후에 다른 프로세스를 실행하는 init 역할을 대체하는 데몬이다.

> 도커에서 systemctl 사용시 '시스템이 초기화 시스템 (PID 1)로 systemd로 부팅되지 않았습니다. 작동 할 수 없습니다'라는 메시지를 볼 수 있다. 가상화 시스템에서 나타나는 현상이다. 이런 경우 service를 대신 사용할 수 있다. WSL2를 사용하는 경우에도 나타난다.

## Target 관리

target 은 기존 SysV init 의 run level 과 같은 개념이고 systemd 의 기본 타겟 명은 default.target 이다. 다중 사용자 모드인 mutl-user.target 의 별칭이다.

**SystemV 리눅스의 runlevel** 리눅스 시스템은 0부터 6까지 부팅레벨이 있으며 시스템 부팅 시에 어떤 레벨로 부팅할지는 /etc/inittab 파일 내용중 id:3:initdefault 부분에서 결정한다.

* 0 - halt (Do NOT set initdefault to this)
* 1 - Single user mode
* 2 - Multiuser, without NFS (The same as 3, if you do not have networking)
* 3 - Full multiuser mode
* 4 - unused
* 5 - X11
* 6 - reboot (Do NOT set initdefault to this)

**부팅레벨 0** 시스템 종료를 의미함, 즉 init 0 명령어 실행시 시스템이 종료된다.

```shell
init 0
```

**부팅레벨 1** 흔히 싱글모드(관리모드)라고 하며 원격로그인 불가이 불가능하고 root계정만 사용할수 있다.

```shell
init 1 #1번 부팅레벨로 진행
```

**부팅레벨2** NFS(Network File System)를 지원하지 않는 멀티유저 실행모드

```shell
init 2
```

**부팅레벨3** NFS(Network File System)를 지원하는 멀티유저 실행모드로 서버용도로 사용되는 리눅스시스템에서 가장 많이 사용되는 레벨이다.

```shell
init 3
```

**부팅레벨 4** 사용하지 않는 실행레벨로 사용자가 직접 지정하여 사용할수있도록 만든 커스텀레벨이다.

```shell
init 4
```

**부팅레벨 5** X윈도우 환경으로 실행된 멀티유저 실행모드이다.

```shell
init 5
```

**부팅레벨 6** 재부팅한다.

```shell
init 6
```

**재부팅**

```shell
reboot
or
shutdown -r now 
or
init 6
```

**종료**

```shell
poweroff
or
halt # 시스템만 종료되고 파워는 OFF되지 않음
or
shutdown -h now
or
init 0
```

### 기본 타겟 확인

```shell
#> systemctl get-default
graphical.target
```

### 타겟 목록 출력

```shell
systemctl list-units --type target
```

Windows WSL로 설치한 우분투에서는 다음과 같은 메시지가 출력되었다. 나중에 확인할 것이다.

```shell
System has not been booted with systemd as init system (PID 1). Can't operate.
Failed to connect to bus: Host is down
```

### 기본 타겟 변경

```shell
systemctl set-default name.target
```

설치시 기본 타겟은 multi-user.target 이며 부팅시 X-Windows 로 로그인하려면 graphical.target 으로 설정한다.

**재부팅하지 않고 타겟 변경**\
systemctl isolate 명령어를 사용하면 재부팅하지 않고 현재 target을 변경할 수 있다.

```shell
sudo systemctl isolate graphical.target
sudo systemctl isolate multi-user.target
```

## service와 systemctl 비교

| service                  | systemctl                                                      | 설명                   |
| ------------------------ | -------------------------------------------------------------- | -------------------- |
| service name start       | systemctl start name.service                                   | 서비스 시작               |
| service name stop        | systemctl stop name.service                                    | 서비스 중지               |
| service name restart     | systemctl restart name.service                                 | 서비스 재시작              |
| service name condrestart | systemctl try-restart name.service                             | 서비스가 구동중이었을 경우에만 재시작 |
| service name reload      | systemctl reload name.service                                  | 설정 재구동               |
| service name status      | systemctl status name.service systemctl is-active name.service | 서비스 구동 여부 확인         |
| service --status-all     | systemctl list-units --type service --all                      | 모든 서비스의 상태 표시        |

## chkconfig와 systemctl 비교

| chkconfig             | systemctl                                                       | 설명                          |
| --------------------- | --------------------------------------------------------------- | --------------------------- |
| chkconfig name on     | systemctl enable name.service                                   | 서비스 활성화(부팅시 자동 구동)          |
| chkconfig name off    | systemctl disable name.service                                  | 서비스 비활성화                    |
| chkconfig --list name | systemctl status name.service systemctl is-enabled name.service | 서비스의 활성화 여부 표시              |
| chkconfig --list      | systemctl list-unit-files --type service                        | 모든 서비스의 현재 활성화 여부 표시        |
| chkconfig --list      | systemctl list-dependencies --after                             | 지정한 target 이후에 시작하는 서비스를 표시 |
| chkconfig --list      | systemctl list-dependencies --before                            | 지정한 target 이전에 시작하는 서비스를 표시 |

## 서비스 관리

### 서비스 상태 확인

```shell
systemctl status 서비스명 
```

### 서비스 구동

```shell
systemctl start mariadb
```

### 서비스 자동 시작

```shell
systemctl enable mariadb
```

### 서비스 목록 보기

```shell
systemctl list-units
```
