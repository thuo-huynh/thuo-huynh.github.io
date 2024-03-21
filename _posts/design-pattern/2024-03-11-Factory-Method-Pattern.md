---
title: "[Design Pattern] Factory Method Pattern"
author: thuohuynh
date: 2024-03-11 16:10:00 +0900
categories: [Design Pattern, Creational Pattern]
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

# Phần 1: Factory Method

**Factory Method** là một creational design pattern. Ở superclass sẽ tạo ra các objects, nhưng ở các subclasses sẽ cho phép ta có thể thay đổi kiểu của object.

![](https://refactoring.guru/images/patterns/content/factory-method/factory-method-en.png)

**Vấn đề** Giả sử bạn đang xây dựng một ứng dụng vận chuyển hoặc logistics chuyên dùng cho xe tải `Truck`. Ứng dụng của bạn trở nên nổi tiếng, bạn nhận được đơn hàng từ các hãng vận chuyển đường biển. Lúc này làm cách nào để đáp ứng nhu cầu của khách hàng đây, thêm class `Ship` song song với class `Truck` hiện có ? Nó có thể giải quyết vấn đề hiện tại nhưng nếu sau này phát sinh thêm một hình thái vận chuyển nữa thì sao ? Nếu vẫn theo cách tiếp cận cũ, code của bạn sẽ trở nên rất to và phức tạp.

![](https://refactoring.guru/images/patterns/diagrams/factory-method/problem1-en.png)

**Giải pháp** Thay vì sử dụng `new` trực tiếp cho constructor tương ứng với phương tiện vận chuyển, ta có thể gọi đến `factory method`, bên trong method này sẽ gọi đến `new` operator để tạo ra kết quả như là một `sản phẩm`.

![](https://refactoring.guru/images/patterns/diagrams/factory-method/solution1.png)

Có thể nhiều người sẽ thấy rằng chỉ đơn thuần là chuyển việc gọi constructor từ một nơi sang một nơi khác. Nhưng trong thực tế ta có thể override factory method và thay đổi lại class của `products` được tạo ra bởi method.

Các factory method của các subclasses chỉ có thể trả về các products với kiểu khác nhau chỉ khi chúng có chung `base class` hoặc `interface`. Bản thân factory method của base class cũng phải có kiểu trả về được định nghĩa như là `interface` chung (được đề cập ở câu trước).

![](https://refactoring.guru/images/patterns/diagrams/factory-method/solution2-en.png)

`Truck` và `Ship` class đều triển khai `Transport` interface, method `deliver` của hai class này sẽ có các cách triển khai khác nhau. Tuy vậy phía client code khi sử dụng `Truck` hoặc `Ship` đều không phân biệt được loại nào với loại nào mà client code sẽ coi chúng đều là `Transport`. Việc các method `deliver` được triển khai như thế nào hoàn toàn không quan trọng đối với client.

![](https://refactoring.guru/images/patterns/diagrams/factory-method/solution3-en.png)

**Cấu trúc**

![](https://refactoring.guru/images/patterns/diagrams/factory-method/structure.png)

1. Product: interface chung của product được tạo ra
2. Tương tự như `Truck` hoặc `Ship` ở ví dụ trên
3. Base class sẽ implement factory method để trả về object với kiểu `Product` (chức năng chính của nó KHÔNG HẲN lúc nào cũng là trả về một object mà nó còn có thể chứa các core business logic).
4. Các subclasses sẽ override factory method.

Ta cũng có thể định nghĩa base class như là một `abstract class` để bắt buộc các subclasses sẽ triển khai lại factory method.

Factory method không nhất thiết phải tạo mới một instance mà có thể lấy từ cache, object pool hoặc các nguồn khác.

**Source code**

```typescript
interface Product {
  operation(): string;
}

class ConcreteProduct1 implements Product {
  public operation(): string {
    return "{Result of the ConcreteProduct1}";
  }
}

class ConcreteProduct2 implements Product {
  public operation(): string {
    return "{Result of the ConcreteProduct2}";
  }
}

abstract class Creator {
  public abstract factoryMethod(): Product;

  public someOperation(): string {
    const product = this.factoryMethod();

    return `Creator: The same creator's code has just worked with ${product.operation()}`;
  }
}

class ConcreteCreator1 extends Creator {
  public factoryMethod(): Product {
    return new ConcreteProduct1();
  }
}

class ConcreteCreator2 extends Creator {
  public factoryMethod(): Product {
    return new ConcreteProduct2();
  }
}

function clientCode(creator: Creator) {
  console.log(
    "Client: I'm not aware of the creator's class, but it still works."
  );
  console.log(creator.someOperation());
}

console.log("App: Launched with the ConcreteCreator1.");
clientCode(new ConcreteCreator1());
console.log("");

console.log("App: Launched with the ConcreteCreator2.");
clientCode(new ConcreteCreator2());
console.log("");
```

# [References]

<https://refactoring.guru/design-patterns/factory-method>
