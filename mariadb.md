# mariadb 설치

설치

```shell
sudo apt-get install -y mariadb-server
```

설치확인

```shell
#> ps -ef | grep mysql
mysql     305343       1  0 03:02 ?        00:00:00 /usr/sbin/mysqld
root      305643  304632  0 03:06 pts/0    00:00:00 grep --color=auto mysql
```

접속하기\
처음 설치했을 때는 비밀번호 없이도 접속이 가능하다.

```shell
mysql -u root -p mysql
```

MariaDB 10.0부터 인증방식에 변화를 줐는데, Unix Socket 방식이다. 시스템의 root가 mysql root 권한을 가지고 접근할 수 있다. 시스템에 mysql이라는 사용자가 있으면 mysql에도 같은 사용자가 있다.

그럼에도 불구하고 mysql root를 시스템의 root와 분리하려면, 즉 mysql_native_password 방식을 사용하고 싶다면 다음과 같이 쿼리를 실행한다.

```sql
update user set plugin = 'mysql_native_password' where User = 'root';
```

이제 password()를 사용하여 암호를 변경한다.

```sql
update user set password = password('1234') where user = 'root';
```

MariaDB 10.4 에도 mysql.user 테이블이 아직 있긴 있는데, 과거 호환을 위해서만 존재한다고 하고, 이제는 mysql.global_priv 를 써야 한다고 한다.

설정파일 위치 /etc/mysql로 이동한다.

```shell
#> pwd
/etc/mysql
#> ls
conf.d  debian-start  debian.cnf  mariadb.cnf  mariadb.conf.d  my.cnf  my.cnf.fallback
#>
```

설정 파일 변경 /etc/mysql/conf.d 디렉터리로 이동한다. mysql.cnf 파일을 열고 다음을 입력한다.

```shell
[mysqld]
character-set-client-handshake = FALSE
character-set-server=utf8mb4
collation-server=utf8mb4_unicode_ci
#lower_case_table_names = 1
autocommit=0

[client]
default-character-set = utf8mb4

[mysql]

default-character-set = utf8mb4

[mysqldump]
default-character-set = utf8mb4
```

다른 IP에서 접근하려면 아래 디렉터리로 이동한다.

```shell
cd /etc/mysql/mariadb.conf.d
```

50-server.cnf 파일을 열고 bind-address를 주석처리한다.

```shell
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
#bind-address            = 127.0.0.1
```

서버를 다시 지삭한다.

datafile 위치 /var/lib/mysql에 위치한다.

```shell
#> pwd
/var/lib/mysql
#> ls
aria_log.00000001  debian-10.3.flag  ib_logfile0  ibdata1  multi-master.info  mysql_upgrade_info  tc.log
aria_log_control   ib_buffer_pool    ib_logfile1  ibtmp1   mysql              performance_schema
#>
```

설치경로 확인 whereis를 사용할 수 있다.

```shell
#> whereis mysql
mysql: /usr/bin/mysql /etc/mysql /usr/share/mysql
```

mysql 중단

```shell
cd /usr/share/mysql
./mysql.server stop 
```

mysql 시작

```shell
./mysql.server start
```

mysql 재시작

```shell
./mysq.server restart 
```

mysql 버전 확인하기

```sql

MariaDB [mysql]> select version()
    -> ;
+----------------------------------+
| version()                        |
+----------------------------------+
| 10.3.30-MariaDB-0ubuntu0.20.04.1 |
+----------------------------------+
1 row in set (0.000 sec)
```

## MariaDB 설정 파일 구조

```shell
 📁etc
   📁mysql 
      📄maradb.cnf
      📄my.cnf->/etc/alternatives/my.cnf
      📁conf.d
        📄mysql.cnf
        📄mysqldump.cnf
      📁mariadb.conf.d
        📄50-client.cnf
        📄50-mysql-clients.cnf
        📄50-mysqld_safe.cnf
        📄50_server.cnf 
```

default option file은 my.cnf 파일이라고 부른다. MariaDB는 옵션 파일들을 아래 리스트의 위치에서 순서대로 찾는다.

10.3.30 버전을 설치했고 /etc/mariadb.cnf 파일에 보면 다음의 순서대로 cnf 파일을 찾는다고 설명이 되어 있다.

```shell
# The MariaDB configuration file
#
# The MariaDB/MySQL tools read configuration files in the following order:
# 1. "/etc/mysql/mariadb.cnf" (this file) to set global defaults,
# 2. "/etc/mysql/conf.d/*.cnf" to set global options.
# 3. "/etc/mysql/mariadb.conf.d/*.cnf" to set MariaDB-only options.
# 4. "~/.my.cnf" to set user-specific options.
#
# If the same option is defined multiple times, the last one will apply.
#
# One can use all long options that the program supports.
# Run program with --help to get a list of available options and with
# --print-defaults to see which it would actually understand and use.

#
# This group is read both both by the client and the server
# use it for options that affect everything
#
[client-server]

# Import all .cnf files from configuration directory
!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mariadb.conf.d/
```

같은 옵션이 적용되어 있다면 마지막 설정이 적용된다고 되어 있다. 따라서 mariadb.conf.d에 설정하는 것이 좋다.





1111
