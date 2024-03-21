---
title: "[Design Pattern] Adapter Pattern"
author: thuohuynh
date: 2024-03-20 13:00:00 +0900
categories: [Design Pattern, Structural Pattern]
tags: [designpattern]
render_with_liquid: false
---

## Mục lục

1. [Phần 1: Factory Method](/posts/Factory-Method-Pattern)
2. [Phần 2: Abstract Factory](/posts/Abstract-Factory-Pattern)
3. [Phần 3: Builder](/posts/Builder-Pattern)
4. [Phần 4: Prototype](/posts/Prototype-Pattern)
5. [Phần 5: Singleton](/posts/Singleton-Pattern)
6. [Phần 6: Adapter](/posts/Adapter-Pattern)
7. [Phần 7: Bridge](/posts/Bridge-Pattern)

# Phần 6: Adapter Pattern

Adapter Pattern cho phép các objects với interface khác nhau có thể hoạt động với nhau.

![](https://refactoring.guru/images/patterns/content/adapter/adapter-en.png)

### Vấn đề

Hãy tưởng tượng bạn đang phát triển một app theo dõi giá cổ phiếu. App này download dữ liệu cổ phiếu từ nhiều nguồn với XML format, hiển thị nó dưới dạng đồ thị. Thế nhưng khi bạn muốn tích hợp thêm một thư viện analytics khác vào thì thư viện này lại sử dụng JSON format.

Bạn hoàn toàn có thể sửa thư viện để nó có thể làm việc với XML format, tuy nhiên điều này sẽ làm ảnh hưởng đến code hiện có của thư viện cũng như có những trường hợp bạn không thể truy cập vào source code của thư viện nên cách tiếp cận này là không thể.

![](https://refactoring.guru/images/patterns/diagrams/adapter/problem-en.png)

### Giải pháp

Bạn có thể tạo ra một `adapter`. Đây là một object đặc biệt, nó convert interface của một object sang dạng mà object khác có thể hiểu được.

Một Adapter có thể wrap lấy một object để che dấu đi sự chuyển đổi kiểu dữ liệu phức tạp ở phía sau. Ví dụ bạn có thể wrap một object làm việc với đơn vị (m, km) để nó có thể xử lí với các đơn vị như (feet hoặc miles). Các object được wrap lại này không nhất thiết phải biết về adapter.

Adapter không những đáp ứng được việc chuyển đổi format dữ liệu mà nó còn giúp các objects với interface khác nhau có thể làm việc được với nhau. Cơ chế như sau:

1. Adapter nhận về một interface, tương thích với một object đang tồn tại.
2. Sử dụng interface này, object có thể gọi các adapter's method.
3. Khi nhận được lời gọi, adapter sẽ chuyển request đến cho object thứ 2 nhưng dưới format và thứ tự mà object này mong muốn.

Có những trường hợp ta hoàn toàn có thể tạo ra two-way adapter có thể convert lời gọi theo 2 chiều. Dưới đây là mô hình cho app tracking stock ở phía trên.

![](https://refactoring.guru/images/patterns/diagrams/adapter/solution-en.png)

### Cấu trúc

**Object adapter** Adapter sẽ implement interface của một object và wrap các objects còn lại.

![](https://refactoring.guru/images/patterns/diagrams/adapter/structure-object-adapter.png)

1. `Client` là class bao hàm business logic
2. `Client Interface` mô tả protocol mà các classes khác phải tuân thủ khi làm việc với Client code.
3. `Service` thường là các thư viện bên ngoài, hoặc các code đã có từ trước. Client không thể dùng nó trực tiếp do interface bị xung đột.
4. `Adapter` là class có khả năng làm việc với cả client và service. Nó triển khai client interface vào wrap service object. Adapter nhận lời gọi từ phía client, chuyển chúng thành các lời gọi theo format mà service object có thể hiểu được.
5. Client không cần quan tâm đến sự tồn tại của adapter. Nhờ điều này việc đưa các adapters mới vào chương trình sẽ không làm ảnh hưởng đến client code. Điều này cũng có lợi khi interface của service class thay đổi thì việc đưa một adapter mới vào chương trình cũng không làm ảnh hưởng gì đến client code.

**Class adapter**

Cách làm này cho phép Adapter class kế thừa trực tiếp từ Client Class và Service Class (điều này chỉ được phép với C++)

![](https://refactoring.guru/images/patterns/diagrams/adapter/structure-class-adapter.png)

1. `Class Adapter` không cần phải wrap bất kì object nào vì nó kế thừa từ client và service. Quá trình adaptation sẽ diễn ra trong method được ghi đè.

### Tính ứng dụng

Nó cho phép ta có thể tạo ra các `middle-layer` class làm việc như một translator giữa code hiện tại và các code sẵn có cũng như các thư việc từ bên ngoài hoặc các class với interface kì cục khác.

### Source code

```ts
/**
 * The Target defines the domain-specific interface used by the client code.
 */
class Target {
  public request(): string {
    return "Target: The default target's behavior.";
  }
}

/**
 * The Adaptee contains some useful behavior, but its interface is incompatible
 * with the existing client code. The Adaptee needs some adaptation before the
 * client code can use it.
 */
class Adaptee {
  public specificRequest(): string {
    return ".eetpadA eht fo roivaheb laicepS";
  }
}

/**
 * The Adapter makes the Adaptee's interface compatible with the Target's
 * interface.
 */
class Adapter extends Target {
  private adaptee: Adaptee;

  constructor(adaptee: Adaptee) {
    super();
    this.adaptee = adaptee;
  }

  public request(): string {
    const result = this.adaptee.specificRequest().split("").reverse().join("");
    return `Adapter: (TRANSLATED) ${result}`;
  }
}

/**
 * The client code supports all classes that follow the Target interface.
 */
function clientCode(target: Target) {
  console.log(target.request());
}

console.log("Client: I can work just fine with the Target objects:");
const target = new Target();
clientCode(target);

console.log("");

const adaptee = new Adaptee();
console.log(
  "Client: The Adaptee class has a weird interface. See, I don't understand it:"
);
console.log(`Adaptee: ${adaptee.specificRequest()}`);

console.log("");

console.log("Client: But I can work with it via the Adapter:");
const adapter = new Adapter(adaptee);
clientCode(adapter);
```

Execution result

```console
Client: I can work just fine with the Target objects:
Target: The default target's behavior.

Client: The Adaptee class has a weird interface. See, I don't understand it:
Adaptee: .eetpadA eht fo roivaheb laicepS

Client: But I can work with it via the Adapter:
Adapter: (TRANSLATED) Special behavior of the Adaptee.
```

# [References]

<https://refactoring.guru/design-patterns/adapter>
