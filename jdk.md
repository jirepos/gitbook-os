# JDK

Java 13 설치

```
sudo apt install openjdk-13-jdk
```

Java 설치 확인

```
java --version
```

Java사 설치된 위치 확인

```
which java
```

환경변수 설정

실제 자바가 설치된 경로를 확인하려면 'readlink' 명령을 사용한다.

```
readlink java
```

다음과 같이 나올 것이다.

```
root@instance-20210604-1640:/etc/alternatives# readlink java
/usr/lib/jvm/java-13-openjdk-amd64/bin/java
root@instance-20210604-1640:/etc/alternatives#
```

결과를 보면 실제 설치경로는 /usr/lib/jvm/java-13 -openjdk-amd64인 것을 알수 있다.

/etc/profile을 열고 JAVA_HOME, PATH, CLASSPATH 변수를 설정한다.

```
export JAVA_HOME=/usr/lib/jvm/java-13-openjdk-amd64/bin/java
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib
```

/etc/profile을 다시 읽어야 한다.

```
source /etc/profile
```

JAVA_HOME 환셩변수 값 확인

```
echo $JAVA_HOMOE
```
