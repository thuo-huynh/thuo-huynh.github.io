---
title: "[System Design] Cân bằng tải"
author: thuohuynh
date: 2024-03-09 00:00:00 +0900
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

# Phần 2: Cân bằng tải (Load Balancing)

Cân bằng tải (load balancing — LB) là một thành phần quan trọng trong bất kỳ hệ thống phân tán nào. Nó đóng vai trò quan trọng trong việc phân tải các request đều đặn đến các máy chủ (Application hoặc Database), giúp tăng tính sẵn sàng của hệ thống.

LB thường thực hiện kiểm tra sức khỏe (health checks) định kỳ của các máy chủ trong hệ thống. Nếu máy chủ có trạng thái khỏe mạnh, LB sẽ chuyển tiếp các request đến máy chủ đó. Ngược lại, nếu máy chủ không khả dụng hoặc không phản hồi (not healthy), LB sẽ loại bỏ máy chủ đó khỏi danh sách và chờ đến khi máy chủ trở lại trạng thái khỏe.

![Mô hình mô tả một hệ thống distributed system được horizontal scaling và sử dụng LB để cân bằng tải.](/assets/img/system-design/load-balance-01.webp)

## Thuật toán Load Balancing

Có nhiều thuật toán để LB điều phối các request, một trong những thuật toán đơn giản nhất là "Round Robin". Nó sắp xếp máy chủ theo một chu kỳ vòng quay và gửi truy vấn đến máy chủ theo thứ tự.

Tuy nhiên, trên thực tế, không có thuật toán LB nào hoàn hảo. Mỗi thuật toán đều có nhược điểm của mình. Việc tìm hiểu về các thuật toán LB có thể là một chủ đề dài và thú vị.

## Mô hình Load Balancing nhiều tầng

Để đạt được tính high availability, có thể áp dụng LB ở nhiều lớp trong hệ thống, như LB cho nhiều DB, LB cho Web-Server, hoặc LB cho Application Server. Mô hình này giúp tránh được điểm lỗi duy nhất (Single point of failure) và cải thiện đáng kể khả năng đáp ứng và sẵn sàng của ứng dụng, đặc biệt khi áp dụng horizontal scaling.

![Mô hình Load Balancing nhiều tầng](/assets/img/system-design/load-balance-03.png)

## Lợi ích của Load Balancing

- Trải nghiệm người dùng tốt hơn và không bị gián đoạn khi có sự cố xảy ra trên một hoặc vài server.
- Tăng tốc độ truy vấn nhờ vào khả năng của nhiều server làm việc đồng thời.
- Dễ dàng scale up hoặc scale down hệ thống mà không làm gián đoạn trải nghiệm người dùng.
- Load Balancer thông minh có thể giúp phát hiện và giải quyết tắc nghẽn trước khi nó xảy ra, từ đó hỗ trợ quá trình scale up hệ thống.
- Giảm căng thẳng cho người quản trị hệ thống bằng cách phân tải công việc cho nhiều server.

Tuy nhiên, LB có thể trở thành điểm lỗi duy nhất nếu quá tải hoặc gặp lỗi phần cứng/phần mềm, dẫn đến sự cố của cả hệ thống. Để khắc phục, nên sử dụng ít nhất 2 LB kết hợp với nhau dưới dạng active-standby để đảm bảo tính sẵn sàng và ổn định của hệ thống.

![Mô hình cụm LB backup cho nhau](/assets/img/system-design/load-balance-02.gif)
