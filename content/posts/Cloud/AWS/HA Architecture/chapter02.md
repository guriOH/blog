---
title: Advanced Load Balancers Theory
date: 2020-01-15 12:55:35
tags:
---
* Sticky Sessions
 - Classic Load Balancer routes each request independently to the registered EC2 instance with the smallest load.
 Sticky sessions allow you to bind a user's session to a specific EC2 instance. This ensures that all requests from the user during the session are sent to the same instance.
 - You can enable Sticky Sessions for application load balancers as well, but the traffic will be sent at the target group level.

* Cross Zone Load Balancing
 
* Path Patterns
 - You can create a listener with rules to forward requests based on the URL path.
 This is known as path-based routing. IF you are running microservices, you can route traffic to multiple back-end services using path-based routing. For example, you can route general requests to one target group and requests to render images to another target group.



* Tips
 - Sticky Sessions enable your users to stick to the same EC2 instance. Can be useful if you are storing information locally to that instance.
 - Cross Zone Load Balancing enables you to load balance across multiple availibility zones.
 - Path patterns allow you to direct traffic to different EC2 instances based on the URL contained in the request