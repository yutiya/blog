title: Redis初试
date: 2015-09-08 11:22:43
categories:
- Redis
- study
tags: Redis
---

## Redis入门     

Redis是一款依据BSD开源协议发行的高性能Key-Value存储系统.通常被成为数据结构服务器,因为值可以是字符串(String),哈希(Map),列表(list),集合(sets)和有序集合(sorted sets)等类型.     

提供一个英文的在线互动学习地址: http://try.redis.io/

命令不需要管大小写,但是键大小写是敏感的     
在redis,和json类似,类似键值对,所以一定要注意理解键key、值value     
编程的都知道,以0开始,所以别被忽悠了      
先列出常用命令     

``` text
    DECR, DECRBY, DEL, EXISTS, EXPIRE, GET, GETSET, HDEL, HEXISTS, HGET, 
    HGETALL, HINCRBY, HKEYS, HLEN, HMGET, HMSET, HSET, HVALS, INCR, INCRBY, 
    KEYS, LINDEX, LLEN, LPOP, LPUSH, LRANGE, LREM, LSET, LTRIM, MGET, MSET, 
    MSETNX, MULTI, PEXPIRE, RENAME, RENAMENX, RPOP, RPOPLPUSH, RPUSH, SADD, 
    SCARD, SDIFF, SDIFFSTORE, SET, SETEX, SETNX, SINTER, SINTERSTORE, 
    SISMEMBER, SMEMBERS, SMOVE, SORT, SPOP, SRANDMEMBER, SREM, SUNION, 
    SUNIONSTORE, TTL, TYPE, ZADD, ZCARD, ZCOUNT, ZINCRBY, ZRANGE, 
    ZRANGEBYSCORE, ZRANK, ZREM, ZREMRANGEBYSCORE, ZREVRANGE, ZSCORE
```

### 增删改查    

设置键值,`SET key value`,有点相当于关系型数据库中的插入     

取值,`GET key`,相当于关系型数据库中的查询     

<!-- more -->

当我们设置值为integer时,使用`INCR key`,会让值自增1     
比如:  

``` code
    SET key 10
    INCR key
    GET key     =>  11
```

`DEL key`删除键值,相当于关系型数据库中的删除,返回1代表删除了一个键     
当使用`INCR key`时,如果键值不存在,会自动设置该键的值为1     
当不同的客户端连接同一数据库,使用下面的操作,就会出现数据不同步,INCR是原子性操作,保证数据同步     

``` code
    x = GET count
    x = x + 1
    SET count x
```

- 客户端A连接,`count = 10`.
- 客户端B连接,`count = 10`.
- 客户端A设置`count = 11`.
- 客户端B设置`count = 11`.

所以请使用`INCR key`将键的值加1     

``` code
    INCR key          //如果键不存在,创建该键值为0,再加1;否则直接加1
    INCRBY key number // 指定给key增加number,value为数字类型
    DECR key          //减,其余一样
    DECRBY key number //减,其余一样

    //自增1,只能用于直接增加键对应的值,在集合中不能使用,比如HINCR命令是没有的
```

当设置了一个键值,使用`EXPIRE key time`设置多少时间(秒)后自动删除该键值.可以使用`TTL key`命令得到自动删除的剩余时间.     

``` code
    SET resource:lock "Redis Demo"
    EXPIRE resource:lock 120        //120秒后自动删除

    TTL resource:lock => 113        //113秒后自动删除
    TTL resource:lock => -2         //键值不存在
    TTL resource:lock => -1         //键值存在,没有设置自动删除时间
```

### list有序集合     

有序集合,数组存储有顺序     

``` code
    RPUSH friends "Alice"       //如果没有list,就创建新的list,并在后面追加值
    RPUSH friends "Bob"         //同上,在后面追加值
                                //friends = ["Alice", "Bob"]

    LPUSH friends "Sam"         //在list前面增加值,friends = ["Sam", "Alice", "Bob"]

    LRANGE friends 0 -1 => 1) "Sam", 2) "Alice", 3) "Bob"   //打印所有
    LRANGE friends 0 1 => 1) "Sam", 2) "Alice"              //打印0到1(闭区间)
    LRANGE friends 1 2 => 1) "Alice", 2) "Bob"              //打印1到2(闭区间)
```

``` code
    LLEN friends => 3           //得到list长度
    LPOP friends => "Sam"       //从list前面移除一个元素
    RPOP friends => "Bob"       //从list后面移除一个元素
```

### set无序集合     

无序集合,数组存储没有顺序     

``` code
    SADD superpowers "flight"   //如果没有该键set,创建set,添加值
    SADD superpowers "reflexes" //添加值,无序set,只管添加,没有顺序

    SREM superpowers "reflexes" //删除set中的该元素

    SISMEMBER key member        //如果member(set元素)在该键中存在返回1,否则返回0

    SMEMBERS key                //输出set中所有元素

    sunion key1 key2 ...keyN    //输出所有指定key(值为无序set)中的所有元素,顺序不固定
```

### 排序集合

类似有序集合,需要给集合中每一个元素指定分数,用于排序

``` code
    ZADD hackers 1940 "Alan Kay"             
            //如果该键对应的排序集合不存在,创建并添加新值
    ZADD hackers 1906 "Grace Hopper"    
    ZADD hackers 1953 "Richard Stallman"
    ZADD hackers 1965 "Yukihiro Matsumoto"
    ZADD hackers 1916 "Claude Shannon"
    ZADD hackers 1969 "Linus Torvalds"
    ZADD hackers 1957 "Sophie Wilson"
    ZADD hackers 1912 "Alan Turing"

    ZRANGE hackers 2 4 => 1) "Claude Shannon", 2) "Alan key", 3) "Richard Stallman"     //按元素指定分数排序后输出集合中2到4的元素(闭区间)
```

### 哈希

就像在json中,key-value的value中,又包含了新的key-value     

``` code
    HSET user:1000 name "John Smith"    //如果没有该键会自动创建,并添加新值
    HSET user:1000 email "john.smith@example.com"
    HSET user:1000 password "s3cret"

    HGETALL user:1000       //得到该键下所有的哈希key-value
    =>
        1) "name"           //哈希键
        2) "John Smith"     //哈希值
        3) "email"
        4) "john.smith@example.com"
        5) "password"
        6) "s3cret"

    HMSET user:1001 name "Mary Jones" password "hidden" email "mjones@example.com"      //设置多个哈希key-value

    HGET user:1001 name => "Mary Jones"     //得到键下哈希key对应的value

    //在哈希key-value中使用原子操作,让数字类型的value增加1
    HSET user:1000 visits 10
    HINCRBY user:1000 visits 1  => 11
    HDEL user:1000 visits               //删除该键下哈希的key
    HINCRBY user:1000 visits 1 => 1
```

### 总结

然后就没有然后了,就到这儿了

