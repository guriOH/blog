---
title: SQS
date: 2020-01-15 16:42:29
tags:
---


# SQS

SQS is web service that gices you access to a message queue that can be used to store message while waiting for a computer to process them.2


# Two type queue

 - Standard Queues (default)
 - Fifo Queues (Complemet Standard queue)


 Tips

  - SQS is pull baed, not pushed baed.
  - Messages are 256 kb in size.
  - Message can be kept in the queue from 1 minute to 14 days; the default retnetion period is 4 days.

  - Visibility Time out is the amount of time that the message is invisible in the SQS queue after a reader picks up that message. Provided the job is processed before the visibility time out expires, the message wil then be deleted from the queue.

  - SQS guarantees that your message will be processed at least once.