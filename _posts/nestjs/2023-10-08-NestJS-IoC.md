---
title: "[NestJS] Introduction IoC, DIP, DI and IoC Container in NestJS"
author: thuohuynh
date: 2024-03-04 00:00:00 +0900
categories: [NestJs]
tags: [nestjs]
render_with_liquid: false
---

Trong **NestJS**, các khái niệm **IoC (Inversion of Control)**, **DI (Dependency Injection)**, **DIP (Dependency Inversion Principle)** và **IoC Container** đóng một vai trò quan trọng trong việc quản lý phụ thuộc và cấu hình ứng dụng. Hãy cùng tìm hiểu chi tiết về chúng:

# 1. IoC (Inversion of Control):
   - **IoC** là một nguyên tắc thiết kế cao cấp, trong đó bạn ủy quyền việc khởi tạo các phần phụ thuộc vào một vùng chứa IoC (trong trường hợp của NestJS, là hệ thống thời gian chạy).
   - Thay vì tự thực hiện việc khởi tạo, bạn chỉ định các phụ thuộc và để **IoC Container** quản lý chúng.
   - **IoC** giúp tách biệt việc khởi tạo và sử dụng phụ thuộc, tạo ra mã dễ đọc và dễ bảo trì.

# 2. IoC Container:
   - **IoC Container** là một framework hoặc thư viện quản lý việc khởi tạo và quản lý các phụ thuộc.
   - Trong NestJS, **IoC Container** được tích hợp sẵn và quản lý các provider (các lớp được đánh dấu là `@Injectable()`).
   - Bạn đăng ký các provider với **IoC Container** trong module của ứng dụng.

# 3. DI (Dependency Injection):
   - **DI** là một mẫu thiết kế, trong đó các phụ thuộc được "đưa vào" (injected) vào các lớp thay vì được khởi tạo bên trong chúng.
   - Trong NestJS, bạn sử dụng **DI** để inject các instances (thường là các service providers) vào các lớp.
   - Ví dụ: Bạn có thể inject `TaskService` vào `TaskController` để sử dụng các phương thức của `TaskService`.

# 4. DIP (Dependency Inversion Principle):
## 4.1. Nguyên tắc DIP:
   - **DIP** tập trung vào việc **đảo ngược phụ thuộc** giữa các thành phần trong ứng dụng.
   - Thay vì các thành phần con phụ thuộc trực tiếp vào thành phần cha, **DIP** khuyến khích sử dụng **abstractions** (giao diện, abstract classes) để giảm sự ràng buộc giữa chúng.
   - Điều này giúp tạo ra sự linh hoạt và dễ dàng thay đổi phụ thuộc mà không ảnh hưởng đến toàn bộ hệ thống.

## 4.2. Áp dụng DIP trong NestJS:
   - Trong NestJS, bạn có thể áp dụng **DIP** bằng cách sử dụng **DI (Dependency Injection)** và **IoC Container**.
   - Các **providers** (service classes) được inject vào các **controllers** và **modules** thông qua **DI**.
   - Thay vì tạo phụ thuộc trực tiếp bằng cách khởi tạo instance của service, bạn chỉ định phụ thuộc trong constructor của controller hoặc module.
   - Ví dụ:

     ```typescript
     // TaskService.ts
     import { Injectable } from '@nestjs/common';

     @Injectable()
     export class TaskService {
       // ...
     }

     // TaskController.ts
     import { Controller, Get } from '@nestjs/common';
     import { TaskService } from './task.service';

     @Controller('tasks')
     export class TaskController {
       constructor(private readonly taskService: TaskService) {}

       @Get()
       findAll(): string {
         return this.taskService.getAllTasks();
       }
     }
     ```

     Trong ví dụ trên, `TaskController` phụ thuộc vào `TaskService` thông qua **DI**, giúp tuân theo nguyên tắc **DIP**.

## 4.3. Lợi ích của DIP:
   - Giảm sự ràng buộc giữa các thành phần.
   - Dễ dàng thay đổi phụ thuộc mà không cần sửa đổi nhiều mã nguồn.
   - Tạo ra mã dễ đọc và dễ bảo trì.

Nhớ rằng **DIP** là một trong những nguyên tắc quan trọng giúp bạn xây dựng ứng dụng NestJS linh hoạt và dễ bảo trì.
