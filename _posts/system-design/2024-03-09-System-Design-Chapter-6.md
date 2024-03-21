---
title: "[System Design] Proxies"
author: thuohuynh
date: 2024-03-21 00:00:00 +0900
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

# Phần 6: Proxies

Proxy server là một server trung gian (intermediary) nằm giữa client và back-end server. Client kết nối với proxy server để yêu cầu (request) những dịch vụ (service) cần thiết như API, web page, file hay connection, v.V… Nói tóm lại, proxy server là một phần của hệ thống phần mềm hoặc phần cứng đóng vai trò trung gian kết nối các yêu cầu từ client tới các thành phần khác trong hệ thống.

Proxy được sử dụng chủ yếu để lọc hoặc ghi log các yêu cầu (filter request, log request) hoặc để thay đổi các request (như thêm hoặc xóa các header, mã hóa/giải mã hay nén các resource). Hoặc một ứng dụng rất hữu ích khác là dùng để cache response rồi trả về cho nhiều request cùng loại, như trong trường hợp nhiều client request tới cùng một resource thì Proxy có thể cache resource đó rồi trả về cho các client mà không cần gọi tới các “remote server” khác.

![Mô hình Proxy Server hoạt động](https://www.seobility.net/en/wiki/images/8/8a/Proxy-Server.png)

## Các kiểu Proxy Server

### 1. Forward Proxy

Một Forward Proxy Server là một máy chủ mà bất kỳ người dùng Internet nào cũng có thể truy cập. Thông thường nó thường được sử dụng để kiểm xoát băng thông hoặc lưu và chuyển tiếp các dịch vụ mạng như DNS hoặc các dịch vụ Web của một nhóm người dùng sử dụng chung Forward Proxy đó. Có hai loại Forward Proxy:

- Proxy ẩn danh (Anonymous Proxy): Proxy server này có thể che dấu các IP thật của các user sử dụng nó khỏi các dịch vụ nó truy cập đến. Proxy ẩn danh có tác dụng tăng độ bảo mật cho người dùng vì địa chỉ IP thực của người dùng có thể được sử dụng để truy dẫn ra thông tin về người dùng và để thâm nhập vào máy tính của người đó. Hoặc dùng để vượt qua tường lửa của các tổ chức hoặc quốc gia. Ngoài ra, proxy ẩn danh còn được dùng để lách qua sự kiểm duyệt Internet của các chính phủ hoặc tổ chức.

- Proxy minh bạch (Trаnspаrent Proxy): Trái ngược với anonymous Proxy, thì proxy này cho phép các dịch vụ truy cập tới biết được IP thật của user bằng các HTTP header (ví dụ như X-Forwarded-For). Ưu điểm của Proxy này là khả năng cache response như website để tăng tốc độ kết nối mạng của những người dùng bên trong máy chủ Proxy.

![](<https://blog.hubspot.com/hubfs/Google%20Drive%20Integration/What%20Is%20a%20Reverse%20Proxy%3F%20(And%20Why%20Does%20It%20Matter%3F)-1.png>)

### 2. Reverse Proxy

Reverse proxy là một proxy server mà khi đứng trước client, có tác dụng chuyển tiếp request đến một hoặc nhiều back-end server và nhận kết quả từ các server này rồi trả về phía client (forwarder), giúp che dấu danh tính của các back-end server bên dưới. Reverse proxy được cài đặt trong một private network của một hoặc nhiều server, và tất cả lưu lượng truy cập đều phải đi qua proxy này.

Thông thường, những server sẽ sử dụng cơ chế reverse proxy này để bảo vệ các ứng dụng có khả năng xử lý HTTP yếu kém hơn. Ví dụ như khả năng xử lý cực lớn các request, những hạn chế về xử lý sự đa dạng của các loại request (các dạng request có thể kể đến như: HTTP(S) 1.x, HTTP(S) 2.x, …) hay khả năng chuyển đổi HTTPS thành HTTP, cache request, xử lý dữ liệu của cookies/session, chia một request thành nhiều request nhỏ hơn rồi tổng hợp lại các response.

![](<https://blog.hubspot.com/hubfs/Google%20Drive%20Integration/What%20Is%20a%20Reverse%20Proxy%3F%20(And%20Why%20Does%20It%20Matter%3F).png>)
