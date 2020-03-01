---
title: Simple Work Flow Service
date: 2020-01-15 16:42:29
tags:
---


# SWF

SWF is a web service that makes it easy to coordinate work across distributed application components.
SWF enables applications for a range of use cases, including media processing, web application back-ends, business process workflows, and analytics pipeliens, to be designed as a coordination of tasks.


# SWF vs SQS
 - SQS has a retention period of up to 14days, with SWF, workflow executions can last up to 1 year.
 - Amozon SWF ensures that a task is assigned only once and is never duplicated. With amazon SQS, you need to handle duplicated message and may also need to ensure that a message is processed only once.
 - Amozon SWF keeps track of all the tasks and events in an application. With amazon SQS, you need to implement your own application-level tracking, especially if your application uses multiple queues.

 # SWF Actors
  - Workflow Starters
  - Deciders : control the flow of activity tasks in a workflow execution.
  - Acitivity Workers - Carry out the activity tasks.
