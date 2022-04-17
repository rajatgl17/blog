---
layout: post
title: Distributed lock implementation using Redis in Java
---

1. Create annotation



        @Target(ElementType.METHOD)
        @Retention(RetentionPolicy.RUNTIME)
        public @interface DistributedLock {

          /*
           * Key against which lock will be acquired
           */
          String key();

          /*
           * Time to wait for lock (if not available readily)
           * Blocking - prefer minimum value
           */
          int lockWaitTime() default 1;

          /*
           * Automatically release lock after time
           * Just for worst case scenario - in case because of any unknown reason unlock doesnt work.
           * For most cases (including exception in method cases) lock will be release after method execution finishes
           * Ideally value should be greater than time taken by the function that is being locked
           */
          int lockForTime() default 1;

          /*
           * Timeunit for both lockWaitTime and lockForTime
           */
          TimeUnit timeunit() default TimeUnit.MILLISECONDS;
        }
      
      
2. Create aspect for annotaion



        @Slf4j
        @Aspect
        @Component
        @AllArgsConstructor
        public class DistributedLockAspect {

          private RedissonClient redissonClient;

          @Around("@annotation(distributedLock)")
          public Object around(final ProceedingJoinPoint joinPoint, final DistributedLock distributedLock) throws Throwable {
              validate(distributedLock);
              final String key = distributedLock.key();

              Object obj;
              final RLock lock = redissonClient.getLock(key);
              final boolean lockResult = lock.tryLock(distributedLock.lockWaitTime(), distributedLock.lockForTime(), distributedLock.timeunit());
              if (lockResult) {
                  try {
                      obj = joinPoint.proceed();
                  } finally {
                      lock.unlock();
                  }
              } else {
                  throw new RuntimeException("Unable to acquire lock for key : " + key);
              }
              return obj;
          }

          private void validate(final DistributedLock distributedLock) {
              if (StringUtils.isBlank(distributedLock.key())) {
                  throw new RuntimeException("No lock key found");
              }

              if (distributedLock.lockForTime() <= 0) {
                  throw new RuntimeException("Invalid lock for time");
              }

              if (distributedLock.lockWaitTime() < 0) {
                  throw new RuntimeException("Invalid lock wait time");
              }
          }
        }
