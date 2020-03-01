---
title: Application Summary
date: 2020-01-15 16:42:29
tags:
---


# SQS
 - SQS is a way to decouple hour infrastructure
 - SQS is pull based, not pushed based.
 - Messages are 256 KB in size.
 - Messages can be kept in the queue from 1 minute to 14 days; the default retention period is 4 days.
 - Standard SQS and FIFO SQS
 - Standard order is not quaranteed and messages can be delivered more than once.
 - FIFO order is strictly maintained and messages are delivered only once.
 - SQS guarantees that your messages will be processed at least once.

# SWF vs SQS
 - SQS has retention period of up to 14 days; with SWF, workflow executions can last up to 1 year.
 - Amazon SWF presents a task-oriented APi, whereas Amazon SQS offers a message-oriented API.
 - Amazon SWF ensures that a task is assigned only once and is never duplicated. With Amazon SQS, you need to handle duplicated messages and may also need to ensure that a message is processed only once.
 - Amazon SWF keeps track of all the tasks and events in an application. With Amazon SQS, need to implement your own application-level tracking, especially if your application uses multiple queues.

# SWF actors
 - Workflow starters
 - Deciders
 - Activity workers

 # SNS Benefits
 - Instantaneous, push-based delivery (no polling)
 - Simple APIs and easy integration with applications
 - Flexble message delivery over multiple transport protocols
 - Inexpensive, pay-as-you-go model with no up-front costs
 - Web-based AWS Management Console offers the simplicithy of a point-and-click interface
 
 # SNS vs SQS
  - Both Messaging service
  - SNS - push
  - SQS - polls(Pulls)

# Elastic Transcoder
 - Just rememver that elastic transcoder is a media transcoder in the cloud. It converts media files from their original source format in to different formats that will play on smartphones, tablets, PCs etc

# API Gateway
- Remeber what api gatway is at a high level
 - API gateway has caching capabilities to increase performance
 - APi gateway is log cost and scales automatically
 - You can throttle API Gateway to prevent attacks
 - You can log results to CloudWatch
 - If you are using Javascript/AJAX that uses multiple domains with API Gateway, ensure that you have enabled CORS on API Gateway

# Kinesis 
 - Know the difference between kinesis streams and kinesis firehose. you will be geiven scenario quesions and you must choose the most relevant service.
 - Understand what kineis Analytic is.

# Cognito
- Federation allows users to authenticate with a Web Identity Provider (Google, Facebook, Amazon)
