---
title: "[Design Pattern] Builder Pattern"
author: thuohuynh
date: 2024-03-13 13:00:00 +0900
categories: [Design Pattern, Creational Pattern, Builder]
tags: [designpattern]
render_with_liquid: false
---

## Mục lục

1. [Phần 1: Factory Method](/posts/Factory-Method-Pattern)
2. [Phần 2: Abstract Factory](/posts/Abstract-Factory-Pattern)
3. [Phần 3: Builder](/posts/Builder-Pattern)
4. [Phần 4: Prototype Pattern](/posts/Prototype-Pattern)

# Phần 3: Builder

Builder cho phép chúng ta có thể tạo ra các objects phức tạp theo từng bước một. Pattern cũng cho phép chúng ta có thể tạo các kiểu objects khác nhau sử dụng cùng một source code.

![](https://refactoring.guru/images/patterns/content/builder/builder-en.png)

### Vấn đề

Hãy tưởng tượng việc chúng ta phải viết các constructor code dài ngoằng, với nhiều dòng code và tham số. Đây là một điều vô cùng tồi tệ khi bạn phải maintain mớ bòng bong đó.

![](https://refactoring.guru/images/patterns/diagrams/builder/problem1.png)

Ví dụ thực tế là khi xây nhà. Bạn cần

- Xây tường
- Xây sàn
- Xây mái
- ...

Nhưng nếu bạn muốn ngôi nhà của mình to hơn, sáng hơn với sân sau hoặc hệ thống sưởi cho mùa đông ? Giải pháp đơn giản nhất đó là kế thừa từ `House` class, sau đó tạo các subclasses để giải quyết hết các nhu cầu trên. Tuy nhiên điều mà bạn nhận lại được đó là một đống các subclasses đi kèm. Cứ khi nào có parameter mới là một lần bạn phải tạo thêm một subclass mới nữa.

Một giải pháp khác nữa đó là tạo một constructor khổng lồ, đáp ứng mọi parameters cho `House` base class. Cách làm này giúp giảm đi đáng kể số lượng các subclasses, nhưng nó lại tạo ra thêm các vấn đề khác đi kèm.

![](https://refactoring.guru/images/patterns/diagrams/builder/problem2.png)

Đa phần trong mọi trường hợp, chúng ta sẽ không sử dụng mọi parameters. Điều này vô tình dẫn đến việc **các lời gọi hàm trông sẽ rất xấu**.

### Giải pháp

Tách construction code ra khỏi class và đưa nó đến các objects khác gọi là các `builders`.

![](https://refactoring.guru/images/patterns/diagrams/builder/solution1.png)

Pattern này sẽ tổ chức quá trình tạo object thành các bước (`buildWalls`, `buildDoor`, ...). Để tạo ra object bạn chỉ cần gọi đến các hàm mà mình cần (tương ứng với đặc tả object mà bạn mong muốn).

Có những lúc các bước xây dựng object sẽ được implement theo những cách khác nhau (với cabin thì `buildWalls` sẽ sử dụng gỗ, còn với nhà thông thường `buildWalls` sẽ sử dụng gạch).

Trong những trường hợp như vậy bạn có thể tạo các classes khác nhau để triển khai tập hợp các `builder methods` theo những cách khác nhau. Khi đó quá trình tạo một object sẽ là tập hợp lời gọi các `builder methods`.

![](https://refactoring.guru/images/patterns/content/builder/builder-comic-1-en.png)

### Director

`Director Class` định nghĩa các bước để thực thi building steps, trong khi builder sẽ cung cấp implementation cho các bước này. Phía client không nhất thiết phải sử dụng `Director Class` mà hoàn toàn có thể gọi các builder methods theo trình tự mà mình mong muốn.

Tuy nhiên `Director Class` có thể là nơi đặt các construction routines để bạn có thể tái sử dụng chúng. Ngoài ra thì `Director Class` cũng giúp chúng ta có thể che dấu đi các bước tạo ra object, client chỉ đơn thuần gọi đến `Director Class` và lấy về object mong muốn.

![](https://refactoring.guru/images/patterns/content/builder/builder-comic-2-en.png)

### Cấu trúc

![](https://refactoring.guru/images/patterns/diagrams/builder/structure.png)

1. Builder interface
2. Các class implement Builder interface
3. Products: các result object
4. Client phải truyền `Builder` vào cho `Director`

### Source code

```typescript
interface Builder {
  producePartA(): void;
  producePartB(): void;
  producePartC(): void;
}

class ConcreteBuilder1 implements Builder {
  private product: Product1;

  constructor() {
    this.reset();
  }

  public reset(): void {
    this.product = new Product1();
  }

  public producePartA(): void {
    this.product.parts.push("PartA1");
  }

  public producePartB(): void {
    this.product.parts.push("PartB1");
  }

  public producePartC(): void {
    this.product.parts.push("PartC1");
  }

  public getProduct(): Product1 {
    const result = this.product;
    this.reset();
    return result;
  }
}

class Product1 {
  public parts: string[] = [];

  public listParts(): void {
    console.log(`Product parts: ${this.parts.join(", ")}\n`);
  }
}

class Director {
  private builder: Builder;

  constructor(builder: Builder) {
    this.builder = builder;
  }

  public buildMinimalViableProduct(): void {
    this.builder.producePartA();
  }

  public buildFullFeaturedProduct(): void {
    this.builder.producePartA();
    this.builder.producePartB();
    this.builder.producePartC();
  }
}

function clientCode() {
  const builder = new ConcreteBuilder1();
  const director = new Director(builder);

  console.log("Standard basic product:");
  director.buildMinimalViableProduct();
  builder.getProduct().listParts();

  console.log("Standard full featured product:");
  director.buildFullFeaturedProduct();
  builder.getProduct().listParts();

  // Use builder pattern without director class
  console.log("Custom product:");
  builder.producePartA();
  builder.producePartC();
  builder.getProduct().listParts();
}

clientCode();
```

# [References]

<https://refactoring.guru/design-patterns/builder>
