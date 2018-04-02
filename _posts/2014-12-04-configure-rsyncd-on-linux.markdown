---
layout: post
title:  "Linux에서 rsyncd 설정하기"
date:   2014-12-04 13:50:00 +0900
categories: server
---
rsync는 delta encoding을 사용하여 사용하여 데이터전송량을 최소화하면서 원격지의 파일과 디렉토리를 동기화해 주는 프로그램이다.

일단 rsync를 통해 데이터를 전송 받으려면 서버에 `rsyncd`라는 Daemon(이하 rsyncd)이 떠 있어야 한다.

서버에 rsyncd을 실행하는 방법에는, 쉘에서 직접 Background로 실행해 두는 방법과 `xinetd`를 사용하여 Service로 실행하는 방법이 있는데 가급적 깔끔한 후자의 방법을 추천한다.

rsyncd를 실행하기 전에 먼저 아래의 설정파일을 생성해야 한다. (Root권한 필요)

`/etc/rsyncd.conf`

```
[myhome]
path=/home/myhome
comment=myhome directory
uid=<UserID>
gid=<GroupID>
use chroot=yes
read only=no
max connections=40
hosts allow=<접근을 허용할 IP들>
```
 
#### 쉘에서 직접 Background로 Daemon을 실행하는 방법
```
rsync --daemon
```

이 경우 서버가 재시작 될 경우엔 수동으로 다시 수행해야 하므로 `/etc/inittab`이나 `/etc/rc.local`을 수정하여 재시작 시에도 자동으로 실행되도록 별도의 작업이 필요하다.

#### xinetd를 사용하여 Service로 실행하는 방법

먼저 아래의 파일에 해당 내용이 존재하는지 확인한다. (Root권한 필요)

`/etc/services`

```
...
rsync           873/tcp                        # rsync
rsync           873/udp                        # rsync
...
```


그리고 아래의 파일을 생성한다. (Root권한 필요)

`/etc/xinetd.d/rsync`

```
service rsync
{
        disable = no
        socket_type     = stream
        wait            = no
        user            = root
        server          = /usr/bin/rsync
        server_args     = --daemon
        log_on_failure  += USERID
}
```

`xinetd`의 설정이 변경되었으므로 재시작하여 변경사항을 적용한다. (Root권한 필요)

```
/etc/init.d/xinetd restart
```
