---
title: "[System Design] Bộ đệm — Caching"
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

# Phần 3: Bộ đệm — Caching

Load balancing giúp hệ thống mở rộng theo chiều ngang bằng cách ngày càng tăng số lượng các máy chủ, nhưng Caching lại là cách để sử dụng tài nguyên (resource) hiệu quả hơn từ đó resource cần cung cấp giảm đi, nhằm tiết kiệm resource và giảm chi phí của hệ thống.

![Cache.](/assets/img/system-design/03/cache.png)

Để làm được điều này, hệ thống sẽ cung cấp một vùng nhớ đệm (cache) để chứa những dữ liệu được request nhiều lần mà không thường xuyên thay đổi vào đó, từ đó hệ thống sẽ lấy dữ liệu từ bộ nhớ cache mà không truy cập vào bộ nhớ chính. Điều này giúp bộ nhớ chính sẽ được giảm tải đi từ đó năng lực của nó tăng lên.

Bộ nhớ cache được sử dụng rất rộng rãi trong mọi thành phần của hệ thống như trong phần cứng (hardware) như RAM/CPU Cache, hệ điều hành (operating systems), trình duyệt web (web browsers), web applications v.v… Bộ nhớ cache giống như một bộ nhớ ngắn hạn (short- term memory) nó có giới hạn về kích cỡ, nhưng thường nhanh hơn rất nhiều lần bộ nhớ chính

Về kiến trúc phần mềm thì Cache có thể tồn tại ở hầu hết các thành phần, nhưng chủ yếu nó tồn tại ở phần thao tác với dữ liệu như Database Layer, giúp trả về dữ liệu nhanh hơn mà không cần tốn chi phí để truy cập vào Database, hay ở phần Front-end để Cache những resource tĩnh như js, css, image file bằng cách dùng CDN.

## Bộ đệm tầng Backend(Application Server Cache):
Ở tầng backend Cache được áp dụng như một bản sao hoặc một phần của DB/Storage, mỗi lần ứng dụng request dữ liệu từ DB/Storage nó sẽ tìm trong bộ nhớ Cache, nếu có ứng sẽ trả về dữ liệu tức thì mà không tốn tài nguyên để query xuống DB/Storage. Tuy nhiên nó chỉ có tác dụng với những dữ liệu được truy cập nhiều lần nhưng ít được thay đổi, ví dụ thông tin cá nhân của người dùng (tên, quê quán/quốc tịch, ngày tháng năm sinh, địa chỉ, số điện thoại…).
Nếu ta có nhu cầu gia tăng khả năng chịu tải bộ nhớ Cache theo mô hình horizontal scaling, khi đó ta sẽ có thêm nhiều node và mỗi node sẽ chứa các dữ liệu của riêng nó. Tuy nhiên điều này sẽ dẫn đến số lượng “cache miss” tăng cao vì LB sẽ tùy chọn ngẫu nhiên 1 node mỗi request từ đó một request sẽ tới nhiều “cache node” khác nhau. Và để giải quyết điều này ta có hai sự lựa chọn là sử dụng duy nhất một nhớ Cache lớn, hoặc phân chia dữ liệu cache trên cụm server với Consistent Hashing

![Cache back-end.](/assets/img/system-design/03/cache-backend.png)

## Bộ đệm tầng Frontend — Content Distribution Network (CDN)
CDN được sử dụng để caching những resource tĩnh trên các website như js, css, images… (static media) mạng lưới CDN sẽ được đặt ở nhiều vùng địa lý khác nhau và dựa theo địa chỉ của từng request mà các static media sẽ được trả về từ trên các máy chủ CDN ở gần nó nhất, nếu không có trên tất cả các máy chủ CDN thì nó sẽ lấy dữ liệu đó từ Back-end Server để trả về và đồng thời cached dữ liệu vừa lấy lên CDN. Nhờ vậy ta có thể tăng tốc khả năng chịu tải cũng như tốc độ của website.
Nếu hệ thống của bạn không đủ lớn để xây dựng một CDN riêng của mình thì bạn cũng có thể sử dụng các dịch vụ CDN sẵn có như như Cloudflare, BootstrapCDN hay Amazon CloudFront v.v…

![Cache front-end.](/assets/img/system-design/03/cache-frontend.png)

## Cache Invalidation
Về bản chất Cache là một bản sao của bộ nhớ chính có thể là DB hay các dạng Storage khác, ví dụ dữ liệu từ DB được update, thì cache phải được update theo (invalidated cache).Cho nên việc đồng bộ dữ liệu giữa bộ nhớ chính và Cache để giải quyết tính consistency của hệ thống là một vấn đề phải giải quyết.

Ta có 3 chiến thuật để giải quyết vấn đề về cache invalidation là:

1. Write-through cache
Là chiến thuật khi có dữ liệu cần thay đổi, hệ thống sẽ ghi dữ liệu cả và Cache hoặc DB đồng thời. Việc này sẽ đảm bảo được việc dữ liệu sẽ luôn luôn consistency bởi vì process sẽ không thành công và phải thực hiện lại nếu một trong hai thao tác ghi vào Cache hoặc ghi vào DB bị lỗi. Tuy nhiên điểm yếu của chiến thuật này là độ trễ cao trong việc ghi dữ liệu, vì đòi hỏi cả hai thao tác ghi từ DB và Cache phải được hoàn thành.

2. Write-around cache
Chiến thuật này khi có dữ liệu cần thay đổi, sẽ chỉ ghi vào DB mà bỏ qua Cache. Điều này sẽ có thể giảm việc Cache bị tràn ngập các thao tác ghi mà rất có thể các dữ liệu này sẽ không bao giờ được sử dụng. Tuy nhiên việc này sẽ gây ra trường hợp “cache miss” nếu client gửi một request được từ một bản ghi mới được ghi vào, nó sẽ gây độ trễ khi đọc vì lúc này dữ liệu phải đọc từ DB sau đó mới được ghi ngược lại vào Cache và trả về cho Client. Và chiến thuật này chỉ phù hợp với những dữ liệu ít thay đổi và hệ thống chấp nhận độ trễ của dữ liệu, vì dữ liệu có thể không consistency trong lúc Cache chưa hết hạn (expired) mà có dữ liệu mới đã được update.

3. Write-back cache
Là khi có dữ liệu cần thay đổi, sẽ chỉ ghi vào bộ nhớ Cache mà bỏ qua DB, dữ liệu chỉ ghi vào DB sau một khoảng thời gian (interval) hoặc với điều kiện nào đó. Điều này sẽ có lợi ích tạo ra cho hệ thống độ trễ thấp (low latency) và lưu lượng cao (high throughput) với những hệ thống chuyên ghi (write-intensive). Tuy nhiên chiến thuật này có độ rủi ro cao về mất mát dữ liệu, bởi vì trước khi dữ liệu được ghi vào DB thì dữ liệu của chúng ta chỉ nằm ở trong Cache, bất kỳ một sự cố nào về Cache đều có thể gây mất mát dữ liệu.

## Cache eviction policies
Ngoài ra ta cũng có các cách chiến thuật xóa dữ liệu không còn cần thiết từ Cache.
- First In First Out (FIFO): Cách dữ liệu được ghi vào Cache trước thì sẽ được ưu tiên xóa đi trước mà không quan tâm tới tần suất hay số lượng truy cập của nó.
- Last In First Out (LIFO): Sẽ ưu tiên xóa những dữ liệu được ghi cũ nhất mà không quan tâm tới tần suất hay số lượng truy cập của nó.
- Least Recently Used (LRU): Sẽ xóa những dữ liệu ít được truy cập gần đây trước tiên.
- Most Recently Used (MRU): Sẽ xóa những dữ liệu được truy cập thường xuyên gần đây nhất.
- Least Frequently Used (LFU): Sẽ xóa những dữ liệu ít được truy cập nhất.
- RR: Sẽ trọn ra một dữ liệu bất kỳ trên Cache để thực hiện thao tác xóa.

Thông thường thì hệ thống sẽ đánh dấu thời điểm hết hạn (expire time) của dữ liệu, nếu quá thời hạn thì Cache sẽ bị xóa đi. Tính toán expire time của Cache cũng là một bài toán khá đau đầu tùy vào logic của hệ thống đang phát triển."