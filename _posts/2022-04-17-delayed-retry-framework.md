---
layout: post
title: Delayed Retry Framework
description: Delayed retry framework and options for delayed messaging queue
author: Rajat Goel
---

## Pseudo Code:

Interface to be implemnted for each operation

    IDelayedRetryProcessor<Request, Response>
        Response process(Request, SomeExtraInfoLikeAttemptNumber)
        void onSucess(Request, Response)
        void onFailure(Request, Exception)
        
Main logic

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

## Options for implementing delayed messaging queue

 - RabbitMQ with delayed queue plugin
	 - If RabbitMQ server restarts, timers are lost => the messages(delayed) are being consumed immediately once the server comes up.
 - RabbitMQ dead letter queue
	 - [https://www.fatalerrors.org/a/rabbitmq-message-tracing-ttl-dead-letter-queue-delay-queue.html](https://www.fatalerrors.org/a/rabbitmq-message-tracing-ttl-dead-letter-queue-delay-queue.html)
 - Redis with RQueue
	 - [https://github.com/sonus21/rqueue](https://github.com/sonus21/rqueue)
	 - As RQueue provides the required features, without any practical constraint such as volume, throughput and availability.
	 - Recommended solution is to use RQueue.
 - AWS SQS
	 - Maximum delay limit of 15 mins.

