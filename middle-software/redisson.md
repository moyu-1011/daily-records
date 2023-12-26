### 1. 锁续期

```JAVA
RLock lock = redissonClient.getLock("anyLock");

## 默认非公平锁；自动续期
    
## 1. 立即返回结果
boolean lock.tryLock();
## 2. 获取锁的最大等待时间
boolean lock.tryLock(long waitTime, TimeUnit unit);
```



监控锁的看门狗超时时间只适用于分布式锁的加锁请求中未明确使用leaseTimeout参数的情况。 这个参数可以用来避免由Redisson客户端节点宕机或其他原因造成死锁的情况。

默认情况下，加锁的时间是30秒。如果加锁的业务没有执行完，有效时间减少1/3（有效期剩下20s时），就会进行一次续期，把锁重置成30秒。那业务的机器万一宕机了呢？宕机了定时任务跑不了，就续不了期，那自然30秒之后锁就解开了。

步骤：

1. 在当前业务进程开始执行时，fork 出一个子进程，用于启动一个定时任务。
2. 该定时任务的定时时间小于锁的过期时间，其会定时查看处理当前请求的业务进程的锁是否已被删除。
3. 如果已被删除，则子进程结束；如果未被删除，说明当前请求的业务还未处理完毕，则将锁的时间重新设置为“原过期时间”。



### 避坑

**1.释放了不持有的锁**

获取锁后业务执行过长，超过了设定的leaseTime释放锁，但最后还是会进finally，释放了其他线程的锁。

```JAVA
finally {
    if (lock.isLocked() && lock.isHeldByCurrentThread()) {
        lock.unlock();
    }
}
```



