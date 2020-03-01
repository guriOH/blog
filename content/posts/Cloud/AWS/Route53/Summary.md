---
title: Route53 summary
date: 2020-01-15 12:55:35
tags:
---

# DNS Summary

- ELBs do not have pre-defined IPv4 addresses; you resolve to them using a DNS name.
- Understand the difference between an Alias Record and a CNAME
- Given the choice, always choose an Alias Record over a CNAME

# Common DNS Types
 - SOA Records
 - NS Records
 - A Records
 - CNAMES
 - MX Records
 - PTR Records

# Routing Rolicies that available with Route53
 - Simple Routing
 - Weighted Routing
 - Latency-based Routing
 - Failover Routing
 - Geolocation Routing
 - Geoproximity Routing (Traffic Flow Only)
 - Multivalue Answer Routing

# Health Checks
 - You can set health checks on individual record sets.
 - If a record set fails a health check it will be removed from Route53 until it passes the health check.
 - You can set SNS notifications to alert you if a health check is failed.

# Simple Routing Policy
 - If you choose the simple routing policy you can only have one record with multiple IP address.
 - If you specify muliple values in a record, Route 53 returns all values to the user in random order.

 # Weighted Routing Policy
  

 # Latency Routing Policy

 # Failover Routing Policy

 # Geolocation Routing Policy

 # Geoproximity Routing (Traffic Flow Only)

  * To use geoproximity routing, you must use Route 53 traffic flow.

 # Multivalue Answer Policy
  - Essentially the same as with Simple based routing, except you get Health Checks.
  
