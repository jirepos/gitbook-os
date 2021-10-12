# iptables

iptables는 리눅스상에서 방화벽을 설정하는 도구로서 커널 2.4 이전 버전에서 사용되던 ipchains를 대신하는 방화벽 도구이다. iptables는 커널상에서의 netfilter 패킷필터링 기능을 사용자 공간에서 제어하는 수준으로 사용할 수 있다.

> 패킷 필터링은 통과하는 패킷의 헤더를 보고 그 전체 패킷의 운명을 결정하는 것을 의미한다. 일반적으로 패킷은 헤더와 데이터를 가진다.

## Chain

iptables는 논리적인 3개의 사슬(Chain)의로 구성되어 있고, 각각 INPUT, OUTPUT, FORWARD라고 한다. 또한 관리자가 정의하여 새로운 사슬도 생성할 수 있다. 기본 사슬의 이름은 대문자이다.

| 체인      | 설명                                      |
| ------- | --------------------------------------- |
| INPUT   | 호스트 컴퓨터를 향하는 모든 패킷                      |
| OUTPUT  | 호스트 컴퓨터에서 발생하는 모든 패킷                    |
| FORWARD | 호스트 컴퓨터가 목적지가 아닌 모든 패킷. 즉, 호스트를 통과하는 패킷 |

## 패킷 필터링 규칙

* 들어 오는 패킷을 순서대로 정의된 규칙(Rule)들을 점검하고 처음 일치되는 규칙을 적용한다.
* 하나의 필터링 규칙에 여러가지 조건으로 지정하면 모든 조건에 일치해야 규칙 적용이 된다.
* 내장된 체인을 모두 검사했지만 일치 항목이 없을 경우에는 체인의 기본 정책이 적용된다. 

## 규칙 타겟

패킷이 규칙과 일치할 대 동작을 취하는 타겟을 지원한다.

| 대상     | 설명                             |
| ------ | ------------------------------ |
| ACCEPT | 허용. 패킷을 허용하는 옵션                |
| DROP   | 파기. 패킷을 완전히 무시                 |
| REJECT | 거부. 패킷을 허용하지 않음. 메시지를 보내면서 거부. |

## 명령어

대문자 옵션(예: -P, -L, -N, -X 등)을 사용하여 전체 사슬에 대한 설정을 할 수 있다. 각 사슬들에 대한 규칙(소문자 옵션을 사용: -s, -d, -p 등)을 설정할 수 있다.

| 옵션 | 설명                 |
| -- | ------------------ |
| -N | 새로운 사슬을 만듬         |
| -A | 지정한 체인의 마지막에 규칙 추가 |
| -D | 지정한 체인의 규칙 삭제      |
| -P | 지정한 체인의 정책을 변경     |
| -I | 지정한 체인의 새로운 규칙을 삽입 |
| -R | 지정한 체인의 규칙을 교체     |

**iptables의 규칙**

| 규칙                 | 설명                                         |
| ------------------ | ------------------------------------------ |
| -s 출발지             | 출발지 IP 주소                                  |
| -d 목적지             | 목적지 IP 주소                                  |
| --sport            | 출발지 포트 번호                                  |
| --dport            | 목적지 포트 번호                                  |
| -j                 | 사용할 대상                                     |
| -p 프로토콜            | 프로토콜 지정(tcp , udp, all)                    |
| -i 인터페이스           | 입력 네트워크 인터페이스(eth0, etho1 등)               |
| -o 인터페이스           | 출력 네트워크 인터페이스(eth0, etho1 등)               |
| -m stat --state 상태 | 패킷의 상태(NEW, ESTABLISHED, RELATED, INVALID) |

## 명령어 구조

```
iptables -A INPUT -s [발신자] --sport [발신자 포트] -d [목적지] --dport [목적지 포트] -j [정책]
```

## 명령어 사용 예

### 기본정책을 ACCEPT로 설정하기

INPUT 체인의 기본 정책을 ACCEPT로 설정한다.

```
iptables -P INPUT ACCEPT
```

OUTPUT 체인의 기본 정책을 ACCEPT로 설정한다.

```
iptables -P OUTPUT ACCEPT
```

FORWARD 체인의 기본 정책을 ACCEPT로 설정한다.

```
iptables -P FORWARD ACCEPT
```

### 적용상태 확인

```
iptables -nvL
```

sudo iptables --list 또는 -L 명령어로 적용된 보안 정책을 확인한다.

```
sudo iptables --list
```

## -m 옵션

'-m' 옵션은 iptables에서 모듈을 확장하기 위해 사용하는 플래그이고 -m state 모듈에는 4가지 상태가 있다.

*   NEW

    이전에 없던 패킷의 새로운 연결요청이 들어옴 
*   ESTABLISHED

    NEW 상태 이후의 패킷.NEW 이후에 패키시들이 오고가는 상태 
*   RELATED

    새로운 연결요청이지만 기존의 연결과 관련된 패킷을 의미
*   INVALID

    이전 상태 중에 어떠한 것도 적용되지 않으면 패킷의 상태를 INVALID로 표시. 

state 옵션의 명령어 구조

```
iptables -[명령어] [체인명] -m state --state 연결상태] -p [프로토콜] -j [정책]
```

아래 명령은 192.168.0.100으로 들어오는 모든 프로토콜의 ESTABLISHED, RELATED 상태이면 허용한다.

```
iptables -I INPUT -m state -d 192.168.0.100 -p all --state ESTABLISHED,RELATED -j ACCEPT
```

아래 명령은 패킷의 상태가 INVALID이면 DROP한다.

```
iptables -I INPUT -m state -d 192.168.0.100 -p all --state INVALID -j DROP
```

### 특정 IP 허용

'-s' 옵션 다음에 아이피를 지정하여 특정 아이피로 들어오는 패킷을 허용한다.

```
iptables -A INPUT -s xxx.xxx.xxx.xxx -j ACCEPT
```

127.0.0.1로 들어오는 패킷을 허용한다.

```
iptables -A INPUT -s 127.0.0.1 -j ACCEPT
```

\-i 옵션을 사용하여 디바이스 ens3에 대한 규칙을 추가한다.

```
sudo iptables -I INPUT 5 -i ens3 -p tcp --dport 443 -m state --state NEW,ESTABLISHED -j ACCEPT
```

## iptables 설정 내용 저장

```
sudo service netfilter-persistent save
```
