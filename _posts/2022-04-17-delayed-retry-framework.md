---
layout: post
title: Delayed Retry Framework
description: Delayed retry framework and options for delayed messaging queue
author: Rajat Goel
---

In distributed systems, operations sometimes fail due to transient issues like network glitches or temporary unavailability of resources. Instead of giving up immediately, it can be beneficial to retry the operation after a delay. A delayed retry framework can help manage such scenarios effectively. In this blog post, I’ll walk you through the core concepts of implementing a delayed retry mechanism in Java.

## Pseudo Code:

The first step is to create an interface that defines the contract for any operation that will be retried.

    IDelayedRetryProcessor<Request, Response>
        Response process(Request, SomeExtraInfoLikeAttemptNumber)
        void onSucess(Request, Response)
        void onFailure(Request, Exception)
        
### Main Logic for Retrying Operations
The core logic of the delayed retry framework is encapsulated in the DelayedRetryable class, which handles the submission and processing of retryable operations.

    DelayedRetryable
        void submitOperation(Request, OperationName)
            procesorBean = load bean from application context with name (operation name bean from config)
            try:
                response = procesorBean.process(Request)
                processorBean.onSuccess(request, response)
            catch:
                if retrybale exception
                    pushToDelayQueue(Request, Delay, 1, operationName)
                else
                    processorBean.onFailure(request, exception)


        void messageCounsmer(Message<Request> message)
            operationName = message.getHeader("X-operationName")
            attempt = message.getHeader("X-Attempt")
            procesorBean = load bean from application context with name (operation name bean from config)
            try:
                response = procesorBean.process(Request)
                processorBean.onSuccess(request, response)
            catch:
                if retrybale exception
                    if AND attemp > maxAttempt
                        processorBean.onFailure(request, MaxAttemptsBreachedException)
                    else
                        pushToDelayQueue(Request, Delay, ++attempt, operationName)
                else
                    processorBean.onFailure(request, exception)


        pushToDelayQueue(Request, Delay, Attempt, operationName)
            - load config for operation name
            - delay calcilation logic   
            - push meesgae to queue, Message = Body(Request) + Headers(operation name, delay, attemp, and others by import/export)

## Options for Implementing the Delayed Messaging Queue
To implement delayed retries, you need a messaging queue that supports delaying messages. Here are some options:

 - RabbitMQ with Delayed Queue Plugin:
	 - If RabbitMQ server restarts, timers are lost => the messages(delayed) are being consumed immediately once the server comes up.
 - RabbitMQ Dead Letter Queue:
	 - [https://www.fatalerrors.org/a/rabbitmq-message-tracing-ttl-dead-letter-queue-delay-queue.html](https://www.fatalerrors.org/a/rabbitmq-message-tracing-ttl-dead-letter-queue-delay-queue.html)
 - Redis with RQueue:
	 - [https://github.com/sonus21/rqueue](https://github.com/sonus21/rqueue)
	 - As RQueue provides the required features, without any practical constraint such as volume, throughput and availability.
	 - Recommended solution is to use RQueue.
 - AWS SQS:
	 - Maximum delay limit of 15 mins.

## Summary
The Delayed Retry Framework is a powerful tool for handling transient failures in distributed systems. By implementing a retry mechanism with customizable delay and max attempts, you can improve the resilience of your applications. Depending on your specific requirements, you can choose from various messaging queue options, each with its own pros and cons. Whether you go with RabbitMQ, Redis, or AWS SQS, the framework allows you to handle failures gracefully and keep your systems running smoothly.

