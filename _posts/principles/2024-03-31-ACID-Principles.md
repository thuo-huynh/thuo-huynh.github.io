---
title: "[Principles] A.C.I.D Principles"
author: thuohuynh
date: 2024-03-31 00:00:00 +0900
categories: [Principles]
tags: [principles]
render_with_liquid: false
---
![A.C.I.D](/assets/img/principles/acid/acid-properties-in-dbms.png)

## Tính chất của ACID

ACID bao gồm bộ 4 thuộc tính, viết tắt tương ứng với các chữ cái:

- A (Atomicity - Tính nguyên tử)
- C (Consistency - Tính nhất quán)
- I (Isolation - Tính cô lập)
- D (Durability - Tính bền vững)

### Atomicity (A)

Atomicity (Tính nguyên tử) là chuỗi hoạt động của database không thể phân chia và không thể rút gọn, sao cho tất cả các bước đều xảy ra hoặc là không gì xảy ra.

> Notes: 
Atomicity is an indivisible and irreducible series of database operations such that either all occurs, or nothing occurs.
{: .prompt-info }

Nếu có một trấnction bị lôi thì transaction đó sẽ được ROLLBACK lại như ban đầu, dữ liệu sẽ không thay đổi, còn nếu không xảy ra lỗi thì transaction sẽ được COMMIT, dữ liệu trong cơ sở dữ liệu sẽ được cập nhật thành công.

Mình có 1 ví dụ ở dưới đây để làm rõ việc tuần thủ và không tuân thủ nguyên tắc Atomic

Như ở dưới, các bạn có thể thấy Remo có $30 trong tài khoản A và muốn chuyển $10 vào tài khoản của Sheero (tài khoản B) có $100. Khi chuyển, tài khoản A giảm xuống $20 nhưng tài khoản B vẫn là $100 vì giao dịch chuyển tiền không thành công như vậy nó không tuân thủ nguyên tắc Atomic

![Transfer failed](/assets/img/principles/acid/acid-properties-in-dbms2.png)

Hình ảnh dưới đây cho thấy cả hai thao tác chuyển tiền và nhận tiền đều được thực hiện thành công. Do đó, giao dịch này là nguyên tử.

![Transfer success](/assets/img/principles/acid/acid-properties-in-dbms3.png)

**Làm sao giúp hệ thống đảm bảo Atomicity?**

Có nhiều cách để đảm bảo nguyên tắc Atomic trong cơ sở dữ liệu:
- **Sử dụng hệ thống quản trị cơ sở dữ liệu (DBMS) tuân thủ ACID**: DBMS tuân thủ ACID có các cơ chế tích hợp sẵn để đảm bảo tính nguyên tử. Đây thường là các đơn giản nhất vì DBMS sẽ tự động xử lý giúp bạn.
- **Triển khai Two-Phase Commit (2PC)**: Đây là một giao thức được sử dụng trong các hệ thống phân tán để đảm bảo rằng một transaction chỉ được cam kết nếu tất cả các nút tham gia đồng ý. Điều này đảm bảo rằng một transaction chỉ được cam kết khi tất cả các nút có dữ liệu được cập nhật và đồng bộ hóa.
- **Sử dụng transaction Logs**: Cơ sở dữ liệu duy trì các transaction log ghi lại tất cả các hành động được thực hiện trong suốt giao dịch. Trong trường hợp xảy ra lỗi, nhật ký có thể được ROLLBACK để giúp hệ thống trở về trạng thái trước khi bắt đầu transaction.


### Consistency (C)

Consistency (Tính nhất quán) có nghĩa là giá trị phải luôn được bảo toàn, yêu cầu rằng tất cả transaction chỉ có thể thay đổi dữ liệu theo những cách được cho phép. Trong DBMS, tính toàn vẹn của dữ liệu phải được duy trì, điều này có nghĩa là nếu có một thay đổi trong cơ sở dữ liệu, thì nó phải luôn được bảo toàn. Trong trường hợp của giao dịch, tính toàn vẹn của dữ liệu rất quan trọng để cơ sở dữ liệu luôn đồng nhất trước và sau giao dịch. Dữ liệu phải luôn chính xác.

> Notes: 
Consistency (or correctness) refers to the requirement that any given database transaction must change affected data only in allowed ways. Any data written to the database must be valid according to all defined rules, including constraints, cascades, triggers, and any combination thereof.
{: .prompt-info }

![Consistency](/assets/img/principles/acid/acid-properties-in-dbms4.png)

Trong hình trên, có ba tài khoản: A, B và C. A thực hiện giao dịch T cho cả B và C. Đầu tiên, A chuyển $50 cho B, khi đó số tiền trong tài khoản A được B đọc là $300 trước khi giao dịch được thực hiện. Sau khi giao dịch T thành công, số tiền khả dụng trong B trở thành $150. Tiếp theo, A chuỷen $20 cho C, tại thời điểm đó, giá trị mà C đọc là $250 (đúng vì giao dịch nợ $50 cho B đã được thực hiện thành công). Cả B và C đều đọc dữ liệu chính xác sau mỗi giao dịch, do đó dữ liệu là nhất quán. Nếu B và C đều đọc giá trị $300, thì dữ liệu sẽ không nhất quán.

**Làm sao giúp hệ thống đảm bảo Consistency**

- Kiểm tra tính hợp lệ của dữ liệu: Trước và sau khi thực hiện một transaction, kiểm tra tính hợp lệ của dữ liệu để đảm bảo rằng nó thỏa mãn tất cả các ràng buộc và quy tắc của hệ thống.
- Rollback và Recovery: Trong trường hợp transaction không thể được hoàn thành thành công và cần phải được hủy bỏ, hệ thống phải có cơ chế để ROLLBACK về trạng thái dữ liệu trước khi transaction bắt đầu.
- Sử dụng database versioning (quản lý phiên bản): Trong một số cơ sở dữ liệu, các phiên bản của một bản ghi có thể được lưu để đảm bảo consistency trong trường hợp có nhiều transaction cùng lúc.


### Isolation  (I)

Isolation (Tính cô lập) đảm bảo các transaction được thực thi một cách độc lập, không phụ thuộc lẫn nhau.

> Notes: 
Isolation: Events within a transaction must be hidden from other transactions running concurrentl
{: .prompt-info }

![Isolation](/assets/img/principles/acid/acid-properties-in-dbms5.png)

Mình có ví dụ này để làm rõ tính Isolation, nếu hai hoạt động đang chạy đồng thời trên hai tài khoản khác nhau, thì giá trị của cả hai tài khoản không nên bị ảnh hưởng. Giá trị nên được duy trì nhất quán. Như bạn có thể thấy trong sơ đồ ở trên đây, tài khoản A đang thực hiện các giao dịch T1 và T2 đối với tài khoản B và C, nhưng cả hai đều thực thi độc lập mà không ảnh hưởng đến nhau. Điều này được gọi là Isolation.

### Durability (D)

Durability (Tính bền vững) đảm bảo rằng những transaction đã commit, kết quả của nó sẽ được lưu trữ vĩnh viễn và không thể thay đổi hoặc mất mát, ngay cả trong trường hợp có lỗi hệ thống, cúp điện hoặc các sự cố khác.

> Notes: 
Durability: Once a transaction has been completed and has committed its results to the database, the system must guarantee that these results survive any subsequent malfunctions.
{: .prompt-info }

Giả sử bạn đang thực hiện một giao dịch trực tuyến để chuyển 1000 đô la từ tài khoản ngân hàng của bạn sang tài khoản của một người thân. Đây coi như một transanction được thực hiện qua Internet và bao gồm nhiều bước như sau:

1. BEGIN transaction
2. Xác nhận số dư hiện tại
3. Giảm 1000 đô la từ tài khoản của bạn
4. Tăng 1000 đô la vào tài khoản của người thân
5. Cập nhật các bản ghi tương ứng
6. COMMIT transaction

Khi tất cả các bước này đã được hoàn thành và transaction đã được commit, tính bền vững đảm bảo rằng các thay đổi đó sẽ được lưu trữ vĩnh viễn trong cơ sở dữ liệu của ngân hàng.

Điều này có nghĩa là ngay cả nếu ngay sau đó xảy ra một lỗi hệ thống hoặc cúp điện, các thay đổi đã được thực hiện sẽ không bị mất và tài khoản của cả hai người sẽ vẫn hiển thị số dư mới đã được cập nhật.

**Làm sao giúp hệ thống đảm bảo Durability**
- Sử dụng transaction logs: Một kỹ thuật phổ biến để đảm bảo tính bền vững là sử dụng log files. Các transaction được ghi vào một tệp log trước khi được cam kết vào cơ sở dữ liệu. Nếu hệ thống gặp lỗi, tệp log có thể được sử dụng để khôi phục lại cơ sở dữ liệu vào trạng thái hợp lệ cuối cùng.
- Sử dụng hệ thống lưu trữ dự phòng: Các cơ sở dữ liệu thường được sao lưu trên nhiều hệ thống lưu trữ hoặc nhiều vị trí địa lý để đảm bảo rằng nếu một hệ thống gặp sự cố, dữ liệu có thể được khôi phục từ một vị trí khác.
- Sử dụng distributed database (cơ sở dữ liệu phân tán): Các cơ sở dữ liệu phân tán lưu trữ dữ liệu trên nhiều nút khác nhau. Khi một transaction được cam kết, nó được sao chép đến tất cả các nút trong hệ thống. Điều này đảm bảo rằng ngay cả khi một vài nút gặp sự cố, dữ liệu vẫn còn tồn tại trên các nút khác và hệ thống có thể tiếp tục hoạt động.
- Sử dụng Write-Ahead Logging (WAL): Trong kỹ thuật này, tất cả các thay đổi được ghi vào log trước khi thực sự được áp dụng vào cơ sở dữ liệu. Điều này đảm bảo rằng trong trường hợp sự cố, các thay đổi gần đây nhất có thể được khôi phục từ log.


# [References]
- <https://200lab.io/blog/acid-la-gi/>
- <https://www.javatpoint.com/acid-properties-in-dbms>
