---
title: "[System Design] Các tính chất chính của một hệ thống phân tán"
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

## Các tính chất chính của một hệ thống phân tán

Hệ thống phân tán (distributed system) là hệ thống phần mềm mà các thành phần cấu tạo nên nó nằm ở trên các máy tính khác nhau được kết nối thành mạng lưới (network). Các máy tính này phối hợp hoạt động với nhau để hoàn thành một nhiệm vụ chung bằng cách trao đổi qua lại các thông điệp (message).

Nói nôm na là hệ thống phân tán là việc hệ thống bạn có nhiều quá trình xử lý độc lập trên nhiều server vật lý khác nhau.

Với các hệ thống doanh nghiệp lớn (enterprise) đòi hỏi sự linh hoạt trong mở rộng bảo trì thì distributed system là một lựa chọn hoàn hảo. Và các tính chất chính để hình thành một hệ thống phân tán là: **Khả năng mở rộng (Scalability)**, **Độ tin cậy (Reliability)**, **Tính khả dụng (Availability)**, **Hiệu suất (Efficiency)** và **Tính mở rộng và khả năng bảo trì (Manageability)**.

## 1. Khả năng mở rộng (Scalability)

**Scalability** là khả năng mở rộng (scaling) của hệ thống (system), quy trình (process) hay mạng lưới (network), với nhu cầu gia tăng về số lượng công việc tăng theo thời gian của mô hình kinh doanh (business model).

Mô hình kinh doanh có thể mở rộng quy mô vì nhiều lý do như gia tăng khối lượng dữ liệu lưu trữ (data storage) hay khối lượng công việc (process/request), ví dụ: số lượng truy cập hay đặt hàng của một hệ thống thương mại điện tử. Và yêu cầu của sự mở rộng phải đạt được nhu cầu này mà không làm giảm hiệu suất, nói chung Scalability là đáp ứng được sử mở rộng hay giảm theo kích thước của hệ thống theo thời gian.

Có hai dạng scaling là **mở rộng theo chiều dọc (vertical scaling)** và **mở rộng theo chiều ngang (horizontal scaling)**.
- **Vertical scaling:** là cách mở rộng server hiện tại bằng cách nâng cấp độ mạnh (power) bằng cách nâng cấp CPU, Ram, Storage, v.V… Vertical-scaling thường bị giới hạn bởi vượt quá khả năng về cấu hình vật lý hiện đại hay độ trễ khi “chẳng may” Server bị downtime để nâng cấp hay deploy hệ thống.
- **Horizontal scaling:** là cách mở rộng bằng cách thêm nhiều Node/Server vào một mạng lưới đang có, làm tăng khả năng chịu tải có hệ thống. Cách làm này rẻ và dễ làm hơn so với Vertical-scaling, đặc biệt là rất dễ dàng downsize cũng như upsize hệ thống.

Một ví dụ của Horizontal-scaling là MongoDB và Cassandra, cả hai đều cung cấp sẵn những phương pháp để scale hệ thống bằng cách thêm nhiều node vào hoặc xóa bớt các node mà không hề có độ trễ (zero downtime). Và một ví dụ khác về Vertical-scaling là MySQL, nó có thể dễ dàng chuyển đổi một Server đang chạy sang một Server mới lớn hơn khỏe hơn, nhưng quá trình có downtime.

## 2. Độ tin cậy (Reliability)

**Reliability** có thể giải thích dân dã rằng đó là độ “lì” 💪 của hệ thống có nghĩa là hệ thống sẽ tiếp tục cung cấp dịch vụ của mình ngay khi có một hoặc nhiều thành phần (phần mềm/phần cứng) của hệ thống bị lỗi. Reliability là thành phần chính trong bất cứ một hệ thống phân tán (distributed system) nào, bởi vì trong một hệ thống như vậy, mọi hỏng hóc của một thành phần nào đó sẽ được thay thế một thành phần đang khỏe mạnh khác, đảm bảo luôn hoàn thành nhiệm vụ yêu cầu.

Ví dụ của độ tin cậy là, một trang thương mại điện tử hay hệ thống ngân hàng (banking) mọi thông tin về giao dịch (transaction) của người dùng sẽ không bao giờ bị hủy do lỗi server đang chạy giao dịch đó, mỗi server bị lỗi sẽ phải được thay thế ngay bởi một bản sao chứa đầy đủ thông tin của server đó.

Để đạt được độ tin cậy, hệ thống phải có chế độ back-up real time của từng thành phần trong hệ thống, đây cũng là một thách thức về mặt kỹ thuật cũng như chi phí của dự án.

## 3. Tính sẵn sàng (Availability)

**Tính sẵn sàng** là thời gian một hệ thống vẫn hoạt động bình thường trong một khoảng thời gian cụ thể, đây là thước đo đơn giản về tỷ lệ phần trăm thời gian mà hệ thống hoạt động liên tục trong một khoảng thời gian bình thường. Ví dụ như một chiếc xe hơi có thể chạy trong nhiều tháng mà không cần bảo trì bảo dưỡng, thì có thể nói là chiếc xe đó có tính availability cao. Nếu chiếc xe hơi đó ngừng hoạt động để đem tới gara để bảo trì, nó được coi là không availability trong thời gian đó.

Sự khác nhau của độ tin cậy (reliability) và tính sẵn sàng (availability):
Nếu hệ thống có tính reliability thì nó chắc chắn sẽ có availability, tuy nhiên hệ thống có tính availability không có nghĩa là nó có tính reliability. Nói một cách khác thì reliability có nghĩa là nó có tính high availability, tuy nhiên vẫn có thể đạt được tính availability với một hệ thống không có tính reliability bằng cách giảm thiểu tối đa thời gian bảo trì, sửa chữa. Hãy lấy một ví dụ, một hệ thống eCommerce có tỷ lệ availability lên đến 99,99% trong hai năm đầu tiên nó bắt đầu, nhưng hệ thống có một lỗi tiềm ẩn về bảo mật mà trong quá trình kiểm thử (testing) không phát hiện ra, khách hàng không hề biết về điều đó và họ vẫn rất hạnh phúc (happy) với hệ thống, cho đến một ngày đẹp trời vào bỗng nhiên lỗi tiềm ẩn đó bị khai thác dẫn đến hệ thống giảm tính sẵn sàng trong một thời gian dài hơn bình thường cho đến khi lỗi được “hot fix” ngay lập tức.

Kỳ thực mà nói một hệ thống có độ tin cậy cao (high reliability) gần như rất khó đạt được trong thực tế, mà hầu như chúng ta chỉ hướng tới một hệ thống có tính sẵn sàng cao (high availability) mà thôi.

## 4. Hiệu suất (Efficiency)

Hiệu suất của một hệ thống phân tán là khả năng chịu tải (high load) và thời gian phản hồi (low latency). Có nghĩa là một hệ thống có khả năng chịu được nhiều request đồng thời với độ trễ thấp là một hệ thống có hiệu suất cao. Thông thường nó được đo đếm bằng số lượng request nó nhận được và phản hồi trong một khoảng thời gian, thường được tính bằng giây. Ví dụ một hệ thống eCommerce có hiệu suất là chịu được 5k lượt đặt hàng trên một giây — 5k order / second, hay 500k lượt người cùng truy cập vào cùng một thời điểm.

## 5. Tính mở rộng và khả năng bảo trì (Manageability)

Một tính chất quan trọng khác của một distributed system đó là khả năng dễ dàng mở rộng và bảo trì của hệ thống, nói cách khác là tốc độ của hệ thống khi thực hiện sửa chữa (repair) hay bảo trì (maintain) khi cần, nếu thời gian trên càng cao thì tính availability càng thấp. Để đạt được điều này, hệ thống cần phải dễ dàng phát hiện lỗi hoặc lỗi tiềm tàng nếu có, khả năng hiểu nhanh được nguyên nhân lỗi (root cause), dễ dàng thực hiện các thay đổi cần thiết để điều chỉnh, hoặc đơn giản chỉ là dễ dàng mở rộng khi cần.

Việc sớm phát hiện cũng như giải quyết vấn đề sớm sẽ làm giảm downtime từ đó tăng tính sẵn sàng của hệ thống đi lên. Ví dụ những ứng dụng doanh nghiệp (enterprise system) có khả năng tự phát hiện lỗi sau đó cô lập và báo cáo nhanh cho người vận hành hệ thống.