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

Nguyên tắc này nhằm tách biệt các class để nếu có lỗi phát sinh do sự thay đổi của bạn, nó sẽ không ảnh hưởng đến các class không liên quan khác.

### Diagram

![Diagram](/assets/img/principles/solid/SD.png)

### Code example:

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

Hàm display đảm nhiệm chức năng hiển thị thông tin user - chức năng hoàn toàn khác với ValidatePersion nên cần tách nó ra thành một Class mới, như sau:

```ts
class DisplayPerson {
    private validate: ValidatePerson;

    constructor(name: string, age: number) {
        this.validate = new ValidatePerson(this.name, this.age);
    }

    display(): void {
        if (
            this.validate.validateName(this.validate.name) &&
            this.validate.validateAge(this.validate.age)
        ) {
            console.log(`Name: ${this.validate.name} and Age: ${this.validate.age}`);
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

Nguyên tắc này nhằm mở rộng hành vi của một class mà không thay đổi hành vi hiện tại của class đó. Điều này nhằm tránh gây ra lỗi trong bất kỳ nơi nào class đó được sử dụng.

### Diagram:

![Diagram](/assets/img/principles/solid/OD.png)

### Source code:

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

>Notes: Let q(x) be a property provable about objects of x of type T. Then q(y) should be provable for objects y of type S where S is a subtype of T.

Khi một `child class` không thể thực hiện các hành động tương tự như `parent class`, điều này có thể gây ra các lỗi.

Nếu bạn có một Class và tạo ra một Class khác từ nó, nó trở thành một parent và Class mới trở thành một child. Child Class nên có khả năng thực hiện mọi thứ mà parent Class có thể làm. Quá trình này được gọi là Inheritance (Kế thừa). Child Class nên có khả năng xử lý các yêu cầu tương tự và cung cấp kết quả tương tự như parent Class hoặc có thể cung cấp một kết quả thuộc cùng một loại.

Hình minh họa cho thấy `parent class` là Sportsman (có thể là bất kỳ loại sport nào). Việc `child class` chấp nhận được vì Powerlifter đó là một loại sport cụ thể và vẫn giữ bản nguyên bản chất của `parent class`.

Nếu `child class` không đáp ứng các yêu cầu này, điều đó có nghĩa là `child class` đã thay đổi hoàn toàn và vi phạm nguyên tắc này. Nguyên tắc này nhằm áp đặt tính nhất quán để `parent class` hoặc `child class` của nó có thể được sử dụng mà không gặp bất kỳ lỗi nào.

### Diagram:

![Diagram](/assets/img/principles/solid/LD.png)

### Code examples:

```ts
class Rectangle {
    protected width: number;
    protected height: number;

    constructor(width: number, height: number) {
        this.width = width;
        this.height = height;
    }

    getWidth(): number {
        return this.width;
    }

    getHeight(): number {
        return this.height;
    }

    setWidth(width: number): void {
        this.width = width;
    }

    setHeight(height: number): void {
        this.height = height;
    }

    getArea(): number {
        return this.width * this.height;
    }
}

class Square extends Rectangle {
    constructor(side: number) {
        super(side, side);
    }

    setWidth(width: number): void {
        super.setWidth(width);
        super.setHeight(width);
    }

    setHeight(height: number): void {
        super.setWidth(height);
        super.setHeight(height);
    }
}

function printArea(rectangle: Rectangle): void {
    console.log(`Area: ${rectangle.getArea()}`);
}
```
Trong ví dụ này, lớp `Square` thừa kế từ `Rectangle`, nhưng khi nó triển khai các phương thức `setWidth` và `setHeight`, nó vi phạm nguyên tắc LSP bằng cách làm thay đổi chiều rộng và chiều cao cùng một lúc.

Trong trường hợp này, để chương trình không vi phạm nguyên tắc LSP, ta phải tạo một `parent class` là `class Shape`, sau đó cho `Square` và `Rectangle` kế thừa `class Shape` này.

```ts
class Shape {
    protected width: number;
    protected height: number;

    constructor(width: number, height: number) {
        this.width = width;
        this.height = height;
    }

    getWidth(): number {
        return this.width;
    }

    getHeight(): number {
        return this.height;
    }

    setWidth(width: number): void {
        this.width = width;
    }

    setHeight(height: number): void {
        this.height = height;
    }

    getArea(): number {
        return this.width * this.height;
    }
}

class Rectangle extends Shape {
    constructor(width: number, height: number) {
        super(width, height);
    }
}

class Square extends Shape {
    constructor(side: number) {
        super(side, side);
    }
}

function printArea(shape: Shape): void {
    console.log(`Area: ${shape.getArea()}`);
}

```

## Interface Segregation Principle (ISP)

![Interface Segregation Principle](/assets/img/principles/solid/I.png)

> Notes: "A client should never be forced to implement an interface that it doesn’t use, or clients shouldn’t be forced to depend on methods they do not use."

Không nên buộc một Client triển khai một Interface mà nó không sử dụng hoặc bắt nó phụ thuộc vào một Method mà nó không dùng đến.

Như vậy nguyên tắc Interface Segregation Principle (ISP) khuyến khích chia nhỏ các interface thành các phần nhỏ để không buộc các lớp phải triển khai các phương thức không liên quan đến nhiệm vụ của chúng.

Việc này làm giảm sự phụ thuộc vào các Method không cần thiết và làm cho mã nguồn linh hoạt, dễ mở rộng và bảo trì hơn.

### Diagram

![Diagram](/assets/img/principles/solid/ID.png)

### Source code

```ts
interface Order {
    processOrder(): void;
    calculateTotal(): number;
    sendConfirmationEmail(): void;
}

class OrderHandler implements Order {
    processOrder(): void {
        console.log("Processing order...");
    }

    calculateTotal(): number {
        console.log("Calculating total...");
        return 100;
    }

    sendConfirmationEmail(): void {
        console.log("Sending confirmation email...");
    }
}
```

Trong ví dụ này, mục đích class `Order` chỉ được dùng để xử lý các order đang có, nhưng nó lại phải đi implement cả `calculateTotal()` và `sendConfirmationEmail()`. Như vậy chúng ta nên tách riêng ra các method này ở các interface khác nhau, để giảm độ phụ thuộc vào method mà nó không cần dùng đến.

```ts
interface OrderProcessor {
    processOrder(): void;
}

interface TotalCalculator {
    calculateTotal(): number;
}

interface EmailSender {
    sendConfirmationEmail(): void;
}

class OrderHandler implements OrderProcessor, TotalCalculator, EmailSender {
    processOrder(): void {
        console.log("Processing order...");
    }

    calculateTotal(): number {
        console.log("Calculating total...");
        return 100;
    }

    sendConfirmationEmail(): void {
        console.log("Sending confirmation email...");
    }
}

const orderHandler: OrderProcessor = new OrderHandler();
orderHandler.processOrder();
```

Trong ví dụ này, chúng ta đã chia nhỏ `Order interface` thành các interface con `(OrderProcessor, TotalCalculator, và EmailSender)`. Mỗi interface con đại diện cho một phần cụ thể của nhiệm vụ đặt hàng.

`OrderHandler` là một lớp thực hiện tất cả các interface con. Điều này cho phép chọn lựa các tính năng cần thiết và tránh triển khai các phương thức không liên quan.

## Dependency Inversion Principle (DIP)

![Dependency Inversion Principle](/assets/img/principles/solid/D.png)

> Notes: "Entities must depend on abstractions, not on concretions. It states that the high-level module must not depend on the low-level module, but they should depend on abstractions."

Trong principles này chúng ta có thể hiểu:
- Các module cấp cao không nên phụ thuộc vào các module cấp thấp mà cả hai nên phụ thuộc vào abtraction.
- Abstraction không nên phụ thuộc vào Detail, mà Detail nên phụ thuộc vào abstraction.

### Diagram

![Diagram](/assets/img/principles/solid/DD.png)

### Source code

```ts
// Presentation
class HtmlFormatter implements OutputFormatter {
  format(data: any): string {
    return "<html>" + JSON.stringify(data) + "</html>";
  }
}

// Presentation
class JsonFormatter implements OutputFormatter {
  format(data: any): string {
    return JSON.stringify(data);
  }
}

//  Interface Usecase
interface OutputFormatter {
  format(data: any): string;
}

// Business Login
class BookExporter {
  private formatter: OutputFormatter;

  constructor(formatter: OutputFormatter) {
    this.formatter = formatter;
  }

  export(book: any): void {
    const data = this.getBookData(book);
    const formattedData = this.formatter.format(data);
    console.log(formattedData);
  }

  private getBookData(book: any): any {
    return { title: book.title, author: book.author };
  }
}

const htmlFormatter = new HtmlFormatter();
const jsonFormatter = new JsonFormatter();

const bookExporterHTML = new BookExporter(htmlFormatter);
bookExporterHTML.export({ title: "The Book", author: "John Doe" });

const bookExporterJSON = new BookExporter(jsonFormatter);
bookExporterJSON.export({ title: "The Book", author: "John Doe" });
```

# [References]

- <https://200lab.io/blog/solid-la-gi-vai-tro-cua-solid-trong-oop/>
- <https://www.freecodecamp.org/news/solid-principles-for-programming-and-software-design/>
- <https://okso.app/showcase/solid>