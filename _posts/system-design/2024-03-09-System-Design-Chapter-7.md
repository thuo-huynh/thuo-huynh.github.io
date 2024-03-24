---
title: "[System Design] Sao lưu và đồng bộ dữ liệu — Redundancy and Replication"
author: thuohuynh
date: 2024-03-08 00:00:00 +0900
categories: [SystemDesign]
tags: [systemdesign]
render_with_liquid: false
---

## Mục lục

1. [Phần 1: Các tính chất chính của một hệ thống phân tán](/posts/System-Design-Chapter-1)
2. [Phần 2: Cân bằng tải (Load Balancing)](/posts/System-Design-Chapter-2)
3. [Phần 3: Bộ đệm — Caching](/posts/System-Design-Chapter-3)
4. [Phần 4: Phân chia dữ liệu — Sharding/Data Partitioning](/posts/System-Design-Chapter-4)
5. [Phần 5: Indexes](/posts/System-Design-Chapter-5)
6. [Phần 6: Proxies](/posts/System-Design-Chapter-6)
7. [Phần 7: Sao lưu và đồng bộ dữ liệu — Redundancy and Replication](/posts/System-Design-Chapter-7)
8. [Phần 8: SQL vs. NoSQL](/posts/System-Design-Chapter-8)
9. [Phần 9: Định lý CAP / CAP Theorem](/posts/System-Design-Chapter-9)
10. [Phần 10: Consistent Hashing](/posts/System-Design-Chapter-10)
11. [Phần 11: Long-Polling vs WebSockets vs Server-Sent Events](/posts/System-Design-Chapter-11)

# Phần 7: Sao lưu và đồng bộ dữ liệu — Redundancy and Replication
 
 ## Redundancy

 Là phương thức sao lưu các dữ liệu quan trọng của hệ thống với mục đích tăng độ tin cậy (Reliability) của hệ thống. Ví dụ dữ liệu được lưu trên duy nhất một Server, nếu Server đó mất có nghĩa là tất cả dữ liệu đó mất, do đó việc tạo ra các bản sao lưu là để giải quyết vấn đề này. Redundancy là một điều kiện để xóa bỏ những “single points of failure” và tạo ra các bản sao lưu lúc cần thiết, ví dụ ta có hai máy chủ Database đang chạy trên production và dữ liệu luôn được sao lưu đồng bộ giữa hai máy chủ, và nếu có một trong hai máy chủ bị down thì ta có thể failover sang máy chủ còn lại. Redundancy nó giống như duplicate lại node/server dùng cho mục đích back-up vậy.

 ## Replication

 Gần tương tự với với Redundancy là ta cũng tạo ra một bản sao lưu dữ liệu của Server chính (Master) sang Server backup (Slave), nhưng khác nhau cơ bản là Replication sẽ update dữ liệu giữa Master và Slave là luôn luôn giống nhau và mọi thời điểm (real time synchronization) để tăng độ tin cậy (reliability), đặc biệt là khả năng chịu lỗi của hệ thống (fault-tolerance) và khả năng truy cập (accessibility). Replication được sử dụng rộng rãi ở các CSDLQH (RDBMS) trong mô hình master/slave, dữ liệu giữa master-slave luôn luôn được đồng bộ, trong trường hợp master down thì slave sẽ lên thay master sau khi master vừa down sống dậy nó sẽ trở thành slave mà sẽ thực hiện đồng bộ dữ liệu chưa được update trong thời gian nó bị down.

 ![](https://miro.medium.com/v2/resize:fit:720/format:webp/1*uDAj61vY_uIp7DQtmJMJ3g.png)