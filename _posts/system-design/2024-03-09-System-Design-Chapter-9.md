---
title: "[System Design] Định lý CAP / CAP Theorem"
author: thuohuynh
date: 2024-03-08 00:00:00 +0900
categories: [SystemDesign]
tags: [systemdesign]
render_with_liquid: false
---

## Mục lục

1. [Phần 1: Các tính chất chính của một hệ thống phân tán](/posts/System-Design-Chapter-1)
2. [Phần 2: Cân bằng tải (Load Balancing)](/posts/System-Design-Chapter-2)
3. [Phần 3: Bộ đệm — Caching](#System-Design-Chapter-3)
4. [Phần 4: Phân chia dữ liệu — Sharding/Data Partitioning](#phần-4-phân-chia-dữ-liệu-shardingdata-partitioning)
5. [Phần 5: Indexes](#phần-5-indexes)
6. [Phần 6: Proxies](#phần-6-proxies)
7. [Phần 7: Sao lưu và đồng bộ dữ liệu — Redundancy and Replication](#phần-7-sao-lưu-và-đồng-bộ-dữ-liệu-redundancy-and-replication)
8. [Phần 8: SQL vs. NoSQL](#phần-8-sql-vs-nosql)
9. [Phần 9: Định lý CAP / CAP Theorem](#phần-9-định-lý-cap--cap-theorem)
10. [Phần 10: Consistent Hashing](#phần-10-consistent-hashing)
11. [Phần 11: Long-Polling vs WebSockets vs Server-Sent Events](#phần-11-long-polling-vs-websockets-vs-server-sent-events)

