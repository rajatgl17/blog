---
layout: post
title: Distributed Lock Implementation Using Redis in Java and Spring
description: Implementing distributed lock in spring framework using redis
author: Rajat Goel
---
In a distributed system, ensuring that a resource is accessed by only one process at a time can be crucial to maintaining data consistency. One common approach to achieving this is by using distributed locks. Redis, with its in-memory data structure store, is a popular choice for implementing distributed locks due to its simplicity and efficiency.

In this blog post, I'll walk you through how to implement a distributed lock using Redis in a Java application with Spring. We'll create a custom annotation for the lock and an aspect to handle the locking mechanism.

## Step 1: Create a Custom Annotation
First, let's define a custom annotation that will be used to specify which methods should be locked.



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
      
      
This annotation allows you to specify the lock key, the time to wait for the lock, and how long the lock should be held. The lockForTime parameter is particularly important as it ensures the lock is automatically released after a specified period, which is a safeguard in case something goes wrong and the lock isn't released manually.

## Step 2: Create an Aspect for the Annotation
Next, we'll create an aspect to handle the locking mechanism using Redis.



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

## How It Works

**Lock Acquisition**: The aspect intercepts any method annotated with @DistributedLock and attempts to acquire a lock using the specified key. The tryLock method attempts to acquire the lock within the lockWaitTime. If successful, it holds the lock for lockForTime.

**Lock Release**: After the method execution completes, whether it succeeds or fails, the lock is released in the finally block, ensuring that it doesn't remain locked indefinitely.

**Validation**: The aspect also performs some basic validation to ensure that the annotation parameters are valid, such as checking if the key is not blank and the times are non-negative.
