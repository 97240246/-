连接redis

```bash
redis-cli -p 6379
```

查看所有的key

```bahs
keys *
```

清空所有key

```bash
flushall #清空所有
flushdb #清空当前数据库
```

set key

```bash
set key value
```

get value

```bash
get key
```

判断key是否存在

```bash
exists key
```

移除当前的key

```bash
move key db
```

设置key过期时间 

```bash
expire key time # 时间单位是秒
```

查看当前key的剩余时间

```bash
ttl key
```

判断key的类型

```bash
type key
```



