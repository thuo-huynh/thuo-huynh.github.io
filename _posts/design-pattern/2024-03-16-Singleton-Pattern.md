---
title: "[Design Pattern] Singleton Pattern"
author: thuohuynh
date: 2024-03-16 9:00:00 +0900
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

# Phần 5: Singleton Pattern

Singleton Pattern cho phép bạn có thể chắc chắn rằng class chỉ có duy nhất một instance song song với đó, nó sẽ cung cấp glbal access point đến instance đó.

![](https://refactoring.guru/images/patterns/content/singleton/singleton.png)

## Vấn đề
1. **Đảm bảo class chỉ có duy nhất một instance**: Tại sao ta cần phải kiểm soát số lượng các instance của một class. Câu trả lời đó là khi instance của class là tài nguyên dùng chung (DB hoặc file)

Đây là cách nó hoạt động: hãy tưởng tượng bạn vừa tạo một object nhưng sau đó, khi bạn quyết định tạo thêm một object khác nữa, thay vì nhận về một object mới tinh bạn chỉ nhận về được object mà bạn đã có trước đó.

Chú ý rằng điều này là không thể với các `constructor` thông thường vì các `constructor` thông thường **sẽ luôn** trả về một **object mới tinh** cho chúng ta

![](https://refactoring.guru/images/patterns/content/singleton/singleton-comic-1-en.png)

2. **Cho phép global access tới instance**: Các biến global rất tiện lợi, tuy nhiên nó cũng tiềm ẩn những nguy cơ nhất định như việc bất kì đoạn code nào có thể truy cập tới nó, thay đổi giá trị của nó từ đó dẫn tới khả năng crash app.

Cũng tương tự như các biến toàn cục, Singleton pattern cho phép chúng ta có thể truy cập đến các objects ở bất cứ đâu trong chương trình. Tuy nhiên nó cũng bảo vệ các objects đó khỏi việc bị ghi đè bởi các đoạn code khác.

Có một vấn đề khác mà chúng ta cần quan tâm ở đây đó là việc chúng ta không hề muốn các đoạn code giải quyết vấn đề thứ nhất (**Đảm bảo class chỉ có duy nhất một instance**) nằm rải rác ở nhiều nơi trong chương trình của chúng ta, tốt hơn hết là giữ nó ở trong một class duy nhất (nhất là khi code của bạn phụ thuộc nhiều vào nó)

## Giải pháp
Có 2 bước

1. Thiết lập sao cho `constructor` trở thành private để tránh việc sử dụng `new` operator ở nhiều chỗ khác nhau
2. Tạo một `static creation method` hoạt động với vai trò như constructor nhưng thực chất là nó sẽ gọi tới `private constructor` đã có trước đó, lưu instance mới tạo vào một `static field`. Những lần gọi hàm creation sau đó đều trả về cached object.

## Thực tế
Chính phủ của một đất nước là một ví dụ điển hình về `Singleton pattern`. Mỗi quốc gia chỉ có một chính phủ duy nhất. Tên gọi "Chính phủ nước ..." giống như một con trỏ, trỏ tới một nhóm người nhất định đang làm việc cho chính phủ của một quốc gia.

## Cấu trúc

![](https://refactoring.guru/images/patterns/diagrams/singleton/structure-en.png)

`Singleton class` với static method là `getInstance` sẽ tạo mới instance nếu nó chưa tồn tại còn nếu nó đã tồn tại thì sẽ luôn trả về instance hiện có chứ không hề tạo mới instance.

## Tính ứng dụng
1. Sử dụng `Singleton Pattern` khi class chỉ nên có duy nhất một instance được sử dụng bởi mọi clients (VD: DB object)
2. Sử dụng `Singleton Pattern` khi bạn muốn kiểm soát nghiêm ngặt các biến global.

## Nhược điểm
Đáng kể nhất đó là khi viết unit test cho Singleton vì ta cần tạo `mock object`. Do `constructor` của Singleton là private cũng như ta không thể ghi đè được static method (với nhiều ngôn ngữ hiện có) thì việc tạo `mock object` cần phải áp dụng một cách làm mới. Hoặc nếu không bạn có thể không viết test hoặc không dùng Singleton Pattern.

## Source code

```ts
class Singleton {
  private static instance: Singleton;

  private constructor() {}

  public static getInstance(): Singleton {
    if (!Singleton.instance) {
      Singleton.instance = new Singleton();
    }

    return Singleton.instance;
  }
}

const clientCode = () => {
  const s1 = Singleton.getInstance();
  const s2 = Singleton.getInstance();

  if (s1 === s2) {
    console.log('Singleton works, both variables contain the same instance.');
  } else {
    console.log('Singleton failed, variables contain different instances.');
  }
}

clientCode();
```

# [References]

<https://refactoring.guru/design-patterns/singleton>
