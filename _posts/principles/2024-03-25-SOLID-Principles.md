---
title: "[Principles] S.O.L.I.D Principles"
author: thuohuynh
date: 2024-03-25 22:00:00 +0900
categories: [Principles]
tags: [principles]
render_with_liquid: false
---

# S.O.L.I.D Principles

Nếu các bạn đã quen thuộc với Lập trình hướng đối tượng (OOP), thì có thể bạn đã từng nghe về các nguyên tắc SOLID.

Hiện nay ở trên mang có rất nhiều vài viết hay về SOLID nhưng mình hiếm khi thấy bất kỳ ví dụ nào có hình ảnh. Điều này khiến việc học hỏi đối với những người học tập bằng hình ảnh như mình cảm thấy hơi khó khăn.
Vì vậy, mục đích chính của bài viết này là giúp bạn hiểu rõ hơn về các nguyên tắc này bằng cách sử dụng hình ảnh minh họa và nhấn mạnh mục tiêu của từng nguyên tắc. 

Năm nguyên tắc phát triển phần mềm này là những hướng dẫn cần tuân theo khi xây dựng phần mềm để nó dễ dàng mở rộng và bảo trì hơn. Chúng được phổ biến bởi Robert C. Martin, một kỹ sư phần mềm, còn được biết tới với biệt danh "Uncle Bob", đã cho ra đời cuốn sách "Design Principles and Design Patterns". Trong cuốn sách này ông nhấn mạnh việc lập trình hướng đối tượng phải đáp ứng được khả năng bảo trì và mở rộng bằng cách đề xuất ra 5 nguyên tắc cơ bản gọi là SOLID - viết tắt của của 5 chữ cái đầu của mỗi nguyên tắc:

![S.O.L.I.D](/assets/img/principles/solid/SOLID.png)

- S - Single Responsibility Principle (Nguyên tắc Đơn Nhiệm)
- O - Open/Closed Principle (Nguyên tắc Mở/Đóng)
- L - Liskov Substitution Principle (Nguyên tắc Thay Thế Liskov)
- I - Interface Segregation Principle (Nguyên tắc Phân Chia Interface)
- D - Dependency Inversion Principle (Nguyên tắc Đảo Ngược Phụ Thuộc)

## Single Responsibility Principle (SRP)

> Notes: "A class should have one and only one reason to change, meaning that a class should have only one job"

![Single Responsibility Principle](/assets/img/principles/solid/S.png)

Nếu một class có nhiều chức năng, điều này tăng khả năng phát sinh lỗi vì việc thay đổi một trong các chức năng của nó có thể ảnh hưởng đến các chức năng khác mà chúng ta không hề hay biết. 

**Kết luận:**
Nguyên tắc này nhằm tách biệt các class để nếu có lỗi phát sinh do sự thay đổi của bạn, nó sẽ không ảnh hưởng đến các class không liên quan khác.

```ts
class ValidatePerson {
    private name: string;
    private age: number;

    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
    }

    validateName(name: string): boolean {
        if (name.length > 3) {
            return true;
        } else {
            return false;
        }
    }

    validateAge(age: number): boolean {
        if (age > 18) {
            return true;
        } else {
            return false;
        }
    }

    display(): void {
        if (this.ValidateName(this.name) && this.ValidateAge(this.age)) {
            console.log(`Name: ${this.name} and Age: ${this.age}`);
        } else {
            console.log('Invalid');
        }
    }
}
```

Hàm tạo hoá đơn display đảm nhiệm chức năng hiển thị thông tin user - chức năng hoàn toàn khác với ValidatePersion nên cần tách nó ra thành một Class mới, như sau:

```ts
class DisplayPerson {
    private name: string;
    private age: number;
    private validate: ValidatePerson;

    constructor(name: string, age: number) {
        this.name = name;
        this.age = age;
        this.validate = new ValidatePerson(this.name, this.age);
    }

    Display(): void {
        if (
            this.validate.validateName(this.name) &&
            this.validate.validateAge(this.age)
        ) {
            console.log(`Name: ${this.name} and Age: ${this.age}`);
        } else {
            console.log('Invalid');
        }
    }
}

```

> Notes:
Lưu ý rằng, việc tách một Class có nhiều nhiệm vụ thành nhiều Class khác nhau không đồng nghĩa với việc tách các phương thức (method) thành từng Class riêng biệt. Mỗi Class đảm trách một nhiệm vụ có thể có nhiều phương thức khác nhau. Việc nhóm các phương thức cùng giải quyết một nhiệm vụ về một Class đòi hỏi kiến thức và kinh nghiệm

## Open/Closed Principle (OCP)

![Open/Closed Principle](/assets/img/principles/solid/O.png)

> Notes: "Objects or entities should be open for extension but closed for modification"

Thay đổi hành vi hiện tại của một class sẽ ảnh hưởng đến tất cả các hệ thống sử dụng class đó.
Nếu bạn muốn class thực hiện nhiều chức năng hơn, cách tiếp cận lý tưởng là thêm vào các chức năng đã tồn tại KHÔNG phải là thay đổi chúng.

**Kết luận:**

Nguyên tắc này nhằm mở rộng hành vi của một class mà không thay đổi hành vi hiện tại của class đó. Điều này nhằm tránh gây ra lỗi trong bất kỳ nơi nào class đó được sử dụng.

```ts
interface Shape {
    calculateArea(): number;
}

class Circle implements Shape {
    private radius: number;

    constructor(radius: number) {
        this.radius = radius;
    }

    calculateArea(): number {
        return Math.PI * this.radius * this.radius;
    }
}

class Rectangle implements Shape {
    private width: number;
    private height: number;

    constructor(width: number, height: number) {
        this.width = width;
        this.height = height;
    }

    calculateArea(): number {
        return this.width * this.height;
    }
}
```

Nếu cần thiết chúng ta có thể tiếp tục mở rộng thêm cách tính diện tích các hình khối khác (tam giác, tứ giác...) bằng cách tiếp tục Implement Interface Shape mà không ảnh hưởng tới các lớp hình học khác. Khi cần chỉnh sửa một lớp cụ thể nào đó thì không cần sửa trong Interface mà chỉ cần sửa trong chính lớp đó.

## Liskov Substitution Principle (LSP)

![Liskov Substitution Principle](/assets/img/principles/solid/L.png)

## Interface Segregation Principle (ISP)

![Interface Segregation Principle](/assets/img/principles/solid/I.png)

## Dependency Inversion Principle (DIP)

![Dependency Inversion Principle](/assets/img/principles/solid/D.png)

## Kết luận

# [References]

- <https://200lab.io/blog/solid-la-gi-vai-tro-cua-solid-trong-oop/>
- <https://www.freecodecamp.org/news/solid-principles-for-programming-and-software-design/>
- <https://okso.app/showcase/solid>