---
title: nodemcu micropython
date: 2024-04-25 15:08:32
tags: 
 - postgresql
---

### 夸实例数据迁移
pg_dump -C -h localhost -U localuser dbname | psql -h pg16 -U remoteuser dbname

如果需要忽略角色信息
pg_dump -O -C -h localhost -U localuser dbname | psql -h pg16 -U remoteuser dbname
or

pg_dump -C -h remotehost -U remoteuser dbname | psql -h localhost -U localuser dbname

using psql or pg_dump to connect to a remote host.

With a big database or a slow connection, dumping a file and transfering the file compressed may be faster.

As Kornel said there is no need to dump to a intermediate file, if you want to work compressed you can use a compressed tunnel

pg_dump -C dbname | bzip2 | ssh  remoteuser@remotehost "bunzip2 | psql dbname"
or

pg_dump -C dbname | ssh -C remoteuser@remotehost "psql dbname"
but this solution also requires to get a session in both ends.