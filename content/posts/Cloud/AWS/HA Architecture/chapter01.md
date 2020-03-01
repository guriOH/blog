---
title: Load Balancers Theory
date: 2020-01-15 12:55:35
tags:
---
* Elastic Load Balancer

 - Typs
   - Application 
     - HTTP and HTTPS
   - Network
     - TCP traffic
     - Use for extreme performance
   - Classic 
     - HTTP/HTTPS/TCP

  - 504 Error means the gateway has timed out. This means that the application not responding within the idle timeout period.
  
- Application Load Balancer
  - designed to handle streaming, real-time, and WebSocket workloads in an optimized fashion. Instead of buffering requests and responses, it handles them in streaming fashion.