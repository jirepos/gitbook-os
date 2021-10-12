# apt-get

Ubuntu 14 이상은 apt 명령어로 간단하게 처리 가능하다.

설치된 전체 패키지 보기

```
apt list --installed
```

특정 패키지 설치 여부 확인

```
apt -qq list gcc
```

패키지 정보 보기 (yum info)

```
apt-cache show 패키지이름
```

설치

```
sudo apt-get install gcc
```

재설치 (yum reinstall)

```
sudo apt-get --reinstall install gcc
```

remove - 패키지만 삭제

```
sudo apt-get remove gcc
```

패키지 검색은 apt-cache 명령어를 사

```
sudo apt-cache search php7
```

설치할 수 있는 패키지 목록 보기

```
apt list
```
