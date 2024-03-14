---
title: "[Design Pattern] Prototype Pattern"
author: thuohuynh
date: 2024-03-14 13:00:00 +0900
categories: [Design Pattern, Creational Pattern]
tags: [designpattern]
render_with_liquid: false
---

## Mục lục

1. [Phần 1: Factory Method](/posts/Factory-Method-Pattern)
2. [Phần 2: Abstract Factory](/posts/Abstract-Factory-Pattern)
3. [Phần 3: Builder](/posts/Builder-Pattern)
4. [Phần 4: Prototype](/posts/Prototype-Pattern)

# Phần 4: Prototype Pattern

**Prototype** là `creational pattern` cho phép ta có thể copy object mà không cần phải phụ thuộc vào class của nó.

![](https://refactoring.guru/images/patterns/content/prototype/prototype.png)

## Vấn đề

Bạn muốn copy một object, điều đầu tiên cần làm đó là tạo một instance mới của class, sau đò duyệt qua toàn bộ các giá trị của các fields thuộc về object hiện có và gán giá trị đó cho object mới tạo. Cách làm này không sai nhưng không thể áp dụng khi object có những `private fields`.

Ngoài ra còn có một vấn đề khác đó là bạn cần phải biết class tương ứng với object vừa tạo, điều này khiến code của bạn sẽ phụ thuộc vào một class. Hơn nữa có thể bạn chỉ biết mỗi interface của object chứ không biết được class cụ thể của nó.

![Copying an object “from the outside” isn’t always possible.](https://refactoring.guru/images/patterns/content/prototype/prototype-comic-1-en.png)

## Giải pháp

`Prototype Pattern` sẽ uỷ thác việc clone object cho chính object được clone. Cụ thể là bạn có thể định nghĩa một interface chung cho các object mà bạn muốn clone. Đơn giản chỉ là định nghĩa `interface` với method `clone`.

Việc triển khai `clone` method là hoàn toàn giống nhau ở các class. Cụ thể là tạo một object mới, sao lưu toàn bộ giá trị của các fields vào object mới. Bản thân mọi ngôn ngữ lập trình cũng cho phép các object có thể truy cập đến các private field của object khác nên ta hoàn toàn có thể sao lưu các `giá trị private` một cách dễ dàng.

Một object hỗ trợ cloning sẽ được gọi là `prototype`.

Khi object của bạn có nhiều fields cũng như các config khác thì việc clone chúng có thể được triển khai ở subclass.

![Pre-built prototypes can be an alternative to subclassing.](https://refactoring.guru/images/patterns/content/prototype/prototype-comic-2-en.png)

## Implementation

![](https://refactoring.guru/images/patterns/diagrams/prototype/structure.png)

(1) Định nghĩa `Prototype interface` với `clone` method

(2) ConcretePrototype class sẽ implement `Prototype` interface, ngoài việc copy object gốc, `clone` method có thể có các xử lí khác nếu object cần clone là nested object.

(3) `Client` có thể tạo ra một bản sao của bất kỳ object nào tuân theo prototype interface.

## Pseudocode

Trong ví dụ này, mẫu Prototype cho phép bạn tạo ra các bản sao chính xác của các đối tượng hình học, mà không cần liên kết mã với các lớp của chúng.

![Cloning a set of objects that belong to a class hierarchy.](https://refactoring.guru/images/patterns/diagrams/prototype/example.png)

## Source code

```typescript
class Prototype {
  public primitive: any;
  public component: object;
  public circularReference: ComponentWithBackReference;

  public clone(): this {
    const clone = Object.create(this);

    clone.component = Object.create(this.component);

    // If object has nested object, change reference of nested object to
    // original object to cloned object

    clone.circularReference = {
      ...this.circularReference,
      prototype: { ...this },
    };

    return clone;
  }
}

class ComponentWithBackReference {
  public prototype;

  constructor(prototype: Prototype) {
    this.prototype = prototype;
  }
}

const clientCode = () => {
  const p1 = new Prototype();
  p1.primitive = 245;
  p1.component = new Date();
  p1.circularReference = new ComponentWithBackReference(p1);

  const p2 = p1.clone();

  if (p1.primitive === p2.primitive) {
    console.log("Primitive is THE SAME");
  } else {
    console.log("Primitive is DIFF");
  }

  if (p1.component === p2.component) {
    console.log("Component is THE SAME");
  } else {
    console.log("Component is DIFF");
  }

  if (p1.circularReference === p2.circularReference) {
    console.log("CircularReference is THE SAME");
  } else {
    console.log("CircularReference is DIFF");
  }

  if (p1.circularReference.prototype === p2.circularReference.prototype) {
    console.log("CircularReferencePrototype is THE SAME");
  } else {
    console.log("CircularReferencePrototype is DIFF");
  }
};

clientCode();
```

# [References]

<https://refactoring.guru/design-patterns/prototype>
