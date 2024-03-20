---
title: "[Architecture] Clean Architecture"
author: thuohuynh
date: 2024-03-18 12:00:00 +0900
categories: [Architecture]
tags: [architecture]
render_with_liquid: false
---

## Mục lục

1. [Phần 1: Clean Architecture](/posts/Clean-Architecture)

# Phần 1: Clean Architecture

## Clean Architecture là gì?

Clean Architecture là một kiến trúc ứng dụng rất nổi tiếng dựa trên nguyên lý loại bỏ sự lệ thuộc giữa các đối tượng cũng như các layer trong ứng dụng. Nguyên lý này kế thừa và phát triển dựa trên Dependency Inversion - nguyên lý nổi tiếng trong SOLID.

Trong kiến trúc Clean Architecture bao gồm 4 layer được đại diện thông qua các vòng tròn đồng tâm. Các vòng tròn ở trong sẽ không hề biết gì về các vòng tròn bên ngoài. Nguyên tắc "hướng tâm" này được minh hoạ như sau:

![](https://blog.cleancoder.com/uncle-bob/images/2012-08-13-the-clean-architecture/CleanArchitecture.jpg)

Từ trong ra ngoài Clean Architecture sẽ bao gồm: Entities, Use Cases, Interface Adapters và Frameworks & Drivers.

Về cơ bản các layer này sẽ làm việc qua thông qua các trừu tượng của nhau (interfaces).

### Entities

Entities là layer trong cùng, cũng là layer quan trọng nhất. Entity chính là các thực thể hay từng đối tượng cụ thể và các rule business logic của nó. Trong OOP, đây chính là Object cùng với các method và properties tuân thủ nguyên tắc Encapsulation - chỉ bên trong Object mới có thể thay đổi trạng thái (State) của chính nó.

VD: Trong object Person thì thuộc tính age không thể bé hơn 1. Nếu cần thay đổi age, chúng ta phải viết hàm public setAge, hàm này cũng chịu trách nhiệm check điều kiện liên quan tới age.

Các business logic của layer Entities sẽ không quan tâm hay lệ thuộc vào các business logic ở các layer bên ngoài như Use Cases. Giả sử với trường hợp người dùng phải từ 18 tuổi trở lên mới được phép tạo tài khoản thì rule thuộc tính Age trong Entities vẫn không đổi.

### Use Cases

Use Cases là layer chứa các business logic ở cấp độ cụ thể từng Use Case (hay application).

VD: Use Case đăng ký tài khoản (tạo mới một Person/Account) sẽ cần tổ hợp một hoặc nhiều Entities tuỳ vào độ phức tạp của Use Case.

Các business logic của Use Case đương nhiên cũng sẽ không quan tâm và lệ thuộc vào việc dữ liệu đến từ đâu, dùng các thư viện nào làm apdapter, dữ liệu thể hiện thế nào,... Vì đấy là nhiệm vụ của layer Interface Adapters.

### Interface Adapters (controllers, gateways, presenters)

Interface Adapters chính là layer phụ trách việc chuyển đổi các format dữ liệu để phù hợp với từng Use Case và Entities. Các format dữ liệu này có thể dùng cho cả bên trong hoặc ngoài ứng dụng.

VD: Thông tin người dùng sẽ có một số thông tin rất nhạy cảm như Email, Phone, Address. Không phải lúc nào dữ liệu cũng về đầy đủ để phục vụ GUI (Web, App). Tương tự với tuỳ vào hệ thống Database mà các adapter phải format dữ liệu hợp lý.

Như vậy dữ liệu đầu vào và ra ở tầng Interface Apdapter chỉ cần đủ và hợp lý. Nó sẽ không quan tâm việc dữ liệu sẽ được hiển thị cụ thể như thế nào cũng như được thu thập như thế nào. Vì đó là nhiệm vụ của tầng Frameworks & Drivers.

### Frameworks & Drivers (devices, web, application, databases)

Framework & Drivers là tầng ngoài cùng, tổ hợp các công cụ cụ thể phục vụ cho từng nhu cầu của end user như: thiết bị (devices), web, application, databases,...

Trên thực tế thì đây là nơi "biết tất cả" cụ thể các tầng là gì thông qua việc chịu trách nhiệm khởi tạo các objects cho các tầng bên trong (hay còn gọi là Setup Dependencies)

> Notes:
> Để các layer trong Clean Architecture có thể làm việc được nhưng lại độc lập với nhau thì chúng sẽ dùng các Interfaces.
> {: .prompt-info }

## Ưu và nhược của Clean Architecture

### Nhược điểm của Clean Architecture

1. **Cồng kềnh và phức tạp**: Điều dễ thấy nhất là Clean Architecture không hề dễ sử dụng, phải viết nhiều lớp (class/object) hơn.
2. **Tính trừu tượng cao**: Vấn đề này gọi là indirect. Trừu tượng càng cao thì tiện cho các developers nhưng sẽ gây ảnh hưởng không nhỏ tới tốc độ thực thi (performance).
3. **Khó tuyển người**: Sử dụng Clean Architecture sẽ cần tuyển dụng developer thấu hiểu về kiến trúc này.

### Ưu điểm của Clean Architecture

1. **Chia để trị rất hiệu quả trong ứng dụng lớn**: Trong Clean Architecture thì code tầng nào thì ở đúng tầng nấy.
2. **Rất dễ maintain và mở rộng**: Việc tìm kiếm bug và lỗi logic sẽ trở nên dễ dàng và nhanh hơn, file code sẽ không nhiều vì chỉ làm đúng việc của nó.
3. **Rất dễ làm Unit Test**: Các logic business của các tầng trong Clean Architecture chính là các Unit Test cần được kiểm thử rất cẩn thận.

## Sử dụng kiến trúc Clean Architecture sao cho hợp lý

Đầu tiên không phải một ứng dụng và sản phẩm công nghệ nào cũng đầy đủ 4 tầng Entities, Use Cases, Interface Adapters và Frameworks & Drivers. Chúng ta có thể linh động tăng hoặc giảm số tầng cho phù hợp.

Chúng ta chỉ cần nhớ rằng thay vì thực hiện các business logic ở một nơi (một class hoặc một hàm duy nhất) thì nên chia chúng thành các layer chịu trách nhiệm riêng biệt. Các layer này độc lập với nhau, không sử dụng trực tiếp các concrete object mà thay vào đó là các Interfaces.

Sau đây sẽ là một đề xuất ứng dụng nguyên lý trên nhưng chỉ có 3 tầng cơ bản: Transport, Business, Repository/Storage.

Áp dụng nguyên tắc độc lập các tầng và "hướng vào trong" của Clean Architecture như với sự đơn giản tối thiểu hơn

![Transport, Business và Storage](https://statics.cdn.200lab.io/2022/05/simple-layers-clean-architecture-1.png)

Ví dụ một business của API Update Product:

![API Update Product](https://statics.cdn.200lab.io/2022/05/example-basic-clean-architecture-update-product-api-1.png)

Ở tầng Storage mình minh hoạ cho 2 trường hợp sử dụng Mongo DB hoặc MySQL, chúng giống nhau về các hàm, tham số và trả về (Interfaces) và chỉ khác implement cho từng hệ DB mà thôi. Hãy nhớ rằng chúng ta không hề gọi trực tiếp vào mà chỉ thông qua interfaces thôi nhé!!

# [References]

- <https://200lab.io/blog/clean-architecture-uu-nhuoc-va-cach-dung-hop-ly/>
- <https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html>
- <https://www.youtube.com/watch?v=QR2PScc_fMA&t=2s>
