---
title: "[Design Pattern] Bridge Pattern"
author: thuohuynh
date: 2024-03-25 22:00:00 +0900
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

# Phần 6: Bridge Pattern

Bridge Pattern cho phép ta chia một class lớn hoặc tập các classes có mối liên hệ gần nhau thành 2 classes, tập riêng biệt, độc lập với nhau.

![](https://refactoring.guru/images/patterns/content/bridge/bridge.png)

### Vấn đề
Ta cùng nhau lấy một ví dụ với class tổng quát là `Shape` với 2 sub-classes là `Circle` và `Square`. Giả sử trong trường hợp ta muốn kết hợp với màu, ví dụ như: `Red` & `Blue` thì ta cần cả thảy 4 sub-classes: `BlueCircle`, `BlueSquare`, `RedCircle`, `RedSquare`.

![](https://refactoring.guru/images/patterns/diagrams/bridge/problem-en.png)

Nếu sau này ta thêm một shape mới như `Triangle` thì ta cần thêm 2 sub-classes đó là `BlueTriangle`, `RedTriangle`. Sau đó nếu thêm màu mới thì ta lại phải thêm 3 sub-classes nữa.

Tổng quát lên, nếu số lượng shape và màu tăng thì số lượng sub-classes sẽ tăng với tốc độ của hàm mũ.

### Giải pháp
Vấn đề này xảy ra là do ta đang cố gắng mở rộng class `Shape` thành 2 nhánh là `color` và `form`. Đây là vấn đề thường gặp với `class inheritance`.

Bridge pattern giải quyết vấn đề này bằng cách thay vì kế thừa sẽ chuyển qua object composition. Tách một nhánh cũ thành một tập class và sub-classes riêng biệt. Class gốc ban đầu sẽ tham chiếu đến class thuộc về nhánh mới này thay vì giữ toàn bộ thuộc tính và behavior của nó trong một class duy nhất.

!()[https://refactoring.guru/images/patterns/diagrams/bridge/solution-en.png]

Lúc này, việc thêm màu mới cho `Shape` không quá khó khăn khi chỉ cần thêm vào list các colors của `Shape` mà thôi. Reference tới `Color` thuộc `Shape` sẽ đóng vai trò như một chiếc cầu nối giữa 2 classes.

### Abstraction & Implementation
`Abstraction` là tầng ở mức cao, nó không thực hiện một hành động thực tế nào cả, thay vào đó nó sẽ chuyển tiếp công việc này cho `tầng implementation` (còn gọi là `tầng platform`).

Khi nói về ứng dụng trong thực tế thì những gì được gọi là `abstraction` thường sẽ được cụ thể hoá thông qua UI. Và việc triển khai sẽ nằm ở API hoặc OS.

Nói một cách tổng quát, ta có thể chia một ứng dụng trong thực tế ra thành 2 nhánh độc lập:

* Có một vài UIs (giao diện cho admin và user thông thường)
* Hỗ trợ một vài APIs khác biệt (app có thể chạy ở Window, MacOS, Linux)

Tuy nhiên trong kịch bản tồi tệ nhất, app có thể trông như một bát mì Ý khổng lồ khi cả tá điều kiện kết nối giữa UI và API tồn tại trong code của bạn.

Việc sửa đổi một app với kiến trúc là monolithic sẽ rất khó vì bạn phải hiểu rõ toàn bộ hệ thống, còn với app được chia modules rõ ràng thì việc sửa đổi sẽ dễ hơn rất nhiều.

!()[https://refactoring.guru/images/patterns/content/bridge/bridge-3-en.png]

Bạn có thể đưa các đoạn code liên quan đến một interface-platform cụ thể vào các classes riêng biệt. Nhưng sẽ có `rất nhiều` classes mới sẽ được sinh ra. Việc kế thừa class cũng sẽ tăng nhanh chóng mặt vì việc thêm UI mới hoặc API mới sẽ đòi hỏi các classes mới.

Chúng ta có thể giải quyết vấn đề trên bằng Bridge pattern. Ta sẽ chia các classes thành 2 nhánh kế thừa:

1. Abstraction: UI layer
2. Implementation: OS's APIs

![](https://refactoring.guru/images/patterns/content/bridge/bridge-2-en.png)

Abstraction object điểu khiển tầng UI, nó sẽ chuyển tiếp công việc xử lí cho các implementation objects. Các implementation objects khác nhau hoàn toàn có thể tráo đổi vai trò cho nhau nếu chúng cùng tuân theo interface chung cũng như cho phép UI hoạt động với Window, Linux.

Kết quả là ta có thể thay đổi UI classes mà không làm ảnh hưởng gì đến API-related classes.

### Cấu trúc

![](https://refactoring.guru/images/patterns/diagrams/bridge/structure-en.png)

1. `Abstraction`: cung cấp high-level control. Nó sẽ chuyển tiếp việc xử lí cấp thấp (low-level) cho `implementation object`
2. `Implementation`: định nghĩa interface cho các class triển khai. Abstraction object chỉ có thể giao tiếp với implementation object thông qua các methods được định nghĩa ở interface này.
3. `Concrete Implementations`: platform-specific code.
4. `Client` chỉ quan tâm tới việc tương tác với abstraction.

### Pseudocode

![](https://refactoring.guru/images/patterns/diagrams/bridge/example-en.png)

### Tính ứng dụng
1. Bridge Pattern giúp ta chia nhỏ Monolithic class thành các nhánh class kế thừa độc lập với nhau. Khi class càng lớn, càng dài thì sẽ càng khó để nắm bắt luồng làm việc, cũng như fix bug cho nó. Ngoài ra việc thêm tính năng mới cũng gặp nhiều khó khăn. Việc chia class thành các nhánh class kế thừa cho phép ta có thể thay đổi được class trong nhánh kế thừa này mà không làm ảnh hưởng đến các classes ở nhánh kế thừa khác.
2. Sử dụng pattern khi bạn muốn mở rộng class theo các nhánh độc lập nhau. Từ đó original class có thể chuyển tiếp các công việc cho các class con trong cây kế thừa thay vì làm trực tiếp mọi việc.

### Source code

```ts
class Abstraction {
  protected implementation: Implementation;

  constructor(implementation: Implementation) {
    this.implementation = implementation;
  }

  public operation(): string {
    const result = this.implementation.operationImplementation();
    return `Abstraction: Base operation with:\n${result}`;
  }
}

class ExtendedAbstraction extends Abstraction {
  public operation(): string {
    const result = this.implementation.operationImplementation();
    return `ExtendedAbstraction: Extended operation with:\n${result}`;
  }
}

interface Implementation {
  operationImplementation(): string;
}

class ConcreteImplementationA implements Implementation {
  public operationImplementation(): string {
    return 'ConcreteImplementationA: Here\'s the result on the platform A.';
  }
}

class ConcreteImplementationB implements Implementation {
  public operationImplementation(): string {
    return 'ConcreteImplementationB: Here\'s the result on the platform B.';
  }
}

function clientCode(abstraction: Abstraction) {
  console.log(abstraction.operation());
}

let implementation = new ConcreteImplementationA();
let abstraction = new Abstraction(implementation);
clientCode(abstraction);

console.log('');

implementation = new ConcreteImplementationB();
abstraction = new ExtendedAbstraction(implementation);
clientCode(abstraction);
```

### Result 
```console
Abstraction: Base operation with:
ConcreteImplementationA: Here's the result on the platform A.

ExtendedAbstraction: Extended operation with:
ConcreteImplementationB: Here's the result on the platform B.
```
# [References]

<https://refactoring.guru/design-patterns/bridge>
