<!--
.. title: 非Root用户，Docker挂载卷后写的文件为Root权限
.. slug: fei-rootyong-hu-dockergua-zai-juan-hou-xie-de-wen-jian-wei-rootquan-xian
.. date: 2019-05-10 09:18:53 UTC+08:00
.. tags:
.. category:
.. link:
.. description:
.. type: text
-->

### description:

执行挂载操作的用户的uid和gid会映射到docker里运行.

`Host uid == container uid`

`Host gid == container gid`


如果Docker里的程序创建了其他用户,那写出来的文件,宿主机
可能没有对应的用户,注意只是根据uid和gid来判断,即使有
相同的uid和gid显示的用户名可能还是不同的.

```sql
SELECT H.uid, H.gid, H.uname,H.gname FROM Host H RIGHT JOIN (
   SELECT uid, gid FROM Container
) C ON H.uid = C.uid AND H.gid = C.gid
```

### Solutions:


```yaml
docker run --user $(id -u):$(id -g) other_options
```

注意如果环境变量没有设置`$UID`和`$GID`,

``` shell
WARNING: The UID variable is not set. Defaulting to a blank string.
WARNING: The GID variable is not set. Defaulting to a blank string.
```
使用如下命令执行

`UID=$(id -u) GID=$(id -g) docker-compose`
```
version: '3'
services:
  db:
    image: image_name
    user: ${UID}:${GID}
```
