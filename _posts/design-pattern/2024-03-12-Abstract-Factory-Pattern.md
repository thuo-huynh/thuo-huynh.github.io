---
title: "[Design Pattern] Abstract Factory Pattern"
author: thuohuynh
date: 2024-03-12 13:00:00 +0900
categories: [Design Pattern, Creational Pattern]
tags: [designpattern]
render_with_liquid: false
---

## Mục lục

1. [Phần 1: Factory Method](/posts/Factory-Method-Pattern)
2. [Phần 2: Abstract Factory](/posts/Abstract-Factory-Pattern)
3. [Phần 3: Builder](/posts/Builder-Pattern)
4. [Phần 4: Prototype](/posts/Prototype-Pattern)

# Phần 2: Abstract Factory

**Abstract Factory** cho phép ta có thể tạo một tập các objects liên quan đến nhau mà không cần phải thông qua các classes cụ thể nào.

![](https://refactoring.guru/images/patterns/content/abstract-factory/abstract-factory-en.png)

### Vấn đề

Giả sử bạn đang dev một shop simulator về đồ nội thất, code của bạn gồm các classes:

1. Các đồ dùng gia đình: `Chair` + `Sofa` + `Coffee Table`
2. Các kiểu dáng: `Modern`, `Victorian`, `ArtDeco`

![](https://refactoring.guru/images/patterns/diagrams/abstract-factory/problem-en.png)

Bạn muốn bán cho khách hàng đúng loại đồ nội thất mà họ muốn, đồng thời đáp ứng kịp thời cho sự thay đổi catalog của nhà cung cấp mà không cần phải thay đổi core code.

![](https://refactoring.guru/images/patterns/content/abstract-factory/abstract-factory-comic-1-en.png)

### Giải pháp

Abstract Factory sẽ xử lí vấn đề bằng cách tạo ra các interfaces cho các đồ dùng (VD: `Chair`, `Sofa`, `Coffee Table`). Sau đó các kiểu dáng sẽ là các class implements các interfaces trên.

![](https://refactoring.guru/images/patterns/diagrams/abstract-factory/solution1.png)

Tiếp theo đó là tạo `Abstract Factory` - bản chất là một interface với các methods tạo ra các đồ nội thất `Chair`, `Sofa`, ... tương ứng là `createChair`, `createSofa`. Các methods này sẽ trả về các `abstract` product type mà ta đã định nghĩa thông qua các interface `Chair`, `Sofa`, ...

Với các kiểu dáng, ta sẽ tạo ra các `Factory` class implement `Abstract Factory` ở trên, với các methods sẽ trả về (`ModernChair`, `ModernSofa` tương ứng với `createChair()` và `createSofa()`).

![](https://refactoring.guru/images/patterns/diagrams/abstract-factory/solution2.png)

![](https://refactoring.guru/images/patterns/content/abstract-factory/abstract-factory-comic-2-en.png)

Do phía client không quan tâm đến class cụ thể của factory, nên ta có thể truyền xuống cho client code (kiểu dáng cũng như factory type).

Phía client sẽ xử lí các loại `Chair` như `VictorianChair` hoặc `ModernChair` như nhau vì đều là `Chair` (abstract interface). Với cách tiếp cận này thì client chỉ có thể biết về method `sitOn()` của `Chair` mà thôi. Hơn nữa `Sofa` và `CoffeeTable` được trả về sẽ luôn đồng loại với `Chair`.

Còn một vấn đề nữa đó là khi client chỉ sử dụng interface của factory thôi thì sao ? Khi đó factory object sẽ được khởi tạo từ ban đầu, bản thân factory type sẽ được lấy từ các biến môi trường hoặc các biến config.

### Cấu trúc

![](https://refactoring.guru/images/patterns/diagrams/abstract-factory/structure.png)

1. **Abstract Product**: các interfaces định nghĩa các product liên quan.
2. **Concrete Product**: triển khai các `abstract products` nhóm theo các kiểu dáng. Mỗi abstract product phải được triển khai toàn bộ các kiểu dáng `Modern`, `ArtDeco`, ...
3. **Abstract Factory**: interface định nghĩa các methods tạo các `Abstract Product`.
4. **Concrete Factory**: triển khai `Abstract Factory`, với các methods tạo ra các products thuộc cùng một kiểu dáng.
5. Dù `Concrete Factory` tạo ra các `Concrete Product` xong chữ kí các hàm creation của nó sẽ phải trả về `Abstract Product`

**Source code**

```typescript
interface AbstractProductA {
  usefulFunctionA(): string;
}

interface AbstractProductB {
  usefulFunctionB(): string;
  anotherUsefulFunctionB(collaborator: AbstractProductA): string;
}

interface AbstractFactory {
  createProductA(): AbstractProductA;
  createProductB(): AbstractProductB;
}

class ConcreteProductA1 implements AbstractProductA {
  usefulFunctionA(): string {
    return "The result of the product A1.";
  }
}

class ConcreteProductA2 implements AbstractProductA {
  usefulFunctionA(): string {
    return "The result of the product A2.";
  }
}

class ConcreteProductB1 implements AbstractProductB {
  usefulFunctionB(): string {
    return "The result of the product B1.";
  }

  anotherUsefulFunctionB(collaborator: AbstractProductA): string {
    const result = collaborator.usefulFunctionA();
    return `The result of the B1 collaborating with the (${result})`;
  }
}

class ConcreteProductB2 implements AbstractProductB {
  usefulFunctionB(): string {
    return "The result of the product B2.";
  }

  anotherUsefulFunctionB(collaborator: AbstractProductA): string {
    const result = collaborator.usefulFunctionA();
    return `The result of the B2 collaborating with the (${result})`;
  }
}

class ConcreteFactory1 implements AbstractFactory {
  createProductA(): AbstractProductA {
    return new ConcreteProductA1();
  }

  createProductB(): AbstractProductB {
    return new ConcreteProductB1();
  }
}

class ConcreteFactory2 implements AbstractFactory {
  createProductA(): AbstractProductA {
    return new ConcreteProductA2();
  }

  createProductB(): AbstractProductB {
    return new ConcreteProductB2();
  }
}

function clientCode(factory: AbstractFactory) {
  const productA = factory.createProductA();
  const productB = factory.createProductB();

  console.log(productB.usefulFunctionB());
  console.log(productB.anotherUsefulFunctionB(productA));
}

console.log("Client: Testing client code with the first factory type...");
clientCode(new ConcreteFactory1());

console.log("");

console.log("Client: Testing client code with the second factory type...");
clientCode(new ConcreteFactory2());
```

# [References]

<https://refactoring.guru/design-patterns/abstract-factory>
