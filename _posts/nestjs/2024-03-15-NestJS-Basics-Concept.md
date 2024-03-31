---
title: "[NestJS] Giới thiệu về các khái niệm cơ bản trong NestJS"
author: thuohuynh
date: 2024-03-15 23:35:00 +0900
categories: [NestJS]
tags: [nestjs]
render_with_liquid: false
---

# Các concepts cơ bản trong Nestjs

## Tổng quan

Theo như document của nestjs ta có 8 khái niệm cơ bản như sau:

- Controllers
- Providers
- Modules
- Middleware
- Exception Filters
- Pipes
- Guards
- Interceptors

3 khái niệm đầu tiên Controllers, Providers, Modules sẽ lần lượt đảm nhiệm các nhiệm vụ liên quan đến routing các request từ client cũng như business logic.

5 khái niệm còn lại đều liên quan đến đường đi của request cũng như response và được minh hoạ như trong sơ đồ dưới đây

![](https://storage.googleapis.com/zenn-user-upload/zs4f5nwd0mf2m95sdpcye34x1gc8)

Các đường chấm màu ghi sẽ là HTTP request, HTTP response và Exception (trong thực tế thì Exception cũng sẽ được trả về dưới hình thức HTTP response). App Module sẽ là root module chứa các modules con ở phía trong nó, trong mỗi module con sẽ lại có các controller và service.

Trên đường đi của Request sẽ là:

- Middleware
- Guard
- Interceptor
- Pipe

Còn với Response sẽ là:

- Interceptor
- Exception Filter (với trường hợp của Exception)

Khi đăng kí (Exception filter, Pipe, Guard, Interceptor) với app, ta sẽ lần lượt có 4 levels như sau:

- Global
- Controller
- Method
- Param

## Controlers

Sẽ được khai báo kèm theo `@Controller`. Vai trò của Controller là nhận request, sau đó route handler xử lý logic chính và trả về response. Controller sẽ sử dụng service được cung cấp từ provider và thuộc về module.

Sử dụng command để tạo controller:

```bash
$ nest g controller <name>
```

Cấu trúc cơ bản của Controller trông như thế này:
```typescript
import { Controller, Get, Post, Body } from '@nestjs/common';
import { CreateCatDto } from './dto/create-cat.dto';
import { CatsService } from './cats.service';
import { Cat } from './interfaces/cat.interface';

@Controller('cats') // @Controller() デコレータの適用と Route の指定
export class CatsController {
  constructor(private catsService: CatsService) {} // 利用する Service が inject される

  @Post() // HTTP メソッドの指定
  async create(@Body() createCatDto: CreateCatDto) { // リクエストの Body を取得
    this.catsService.create(createCatDto); // 受け取った値を Service に渡す
  }

  @Get()
  async findAll(): Promise<Cat[]> {
    return this.catsService.findAll(); // Service から得た値をレスポンスとして返す
  }
}
```

Để sử dụng Controller, mình sẽ đăng ký vào Module:

```typescript
import { Module } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Module({
  controllers: [CatsController], // Controller の登録
  providers: [CatsService],
})
export class CatsModule {}
```

## Providers

Sẽ được khai báo kèm theo `@Injectable()` với mục đích dùng cho DI. Thông thường đây sẽ là nơi thực hiện các task được uỷ nhiệm từ phía controller.

Dưới đây là một service được sử dụng như Provider.

Sử dụng command để tạo service:

```bash 
$ nest g service <name>
```

Cấu trúc cơ bản của 1 service sẽ như thế này:

```typescript
import { Injectable } from '@nestjs/common';
import { Cat } from './interfaces/cat.interface';

@Injectable() // @Injectable() デコレータの適用
export class CatsService {
  private readonly cats: Cat[] = [];

  create(cat: Cat) {
    // サービスが提供するビジネスロジックを定義
    this.cats.push(cat);
  }

  findAll(): Cat[] {
    return this.cats;
  }
}
```

Để module đó có thể sài được service, thì mình phải đăng kí như sau:

```typescript
import { Module } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Module({
  controllers: [CatsController],
  providers: [CatsService], // Service の登録
})
export class CatsModule {}
```
## Middleware

Là hàm được gọi phía trước route, có khả năng truy cập đến `Request Object`, `Response Object`. Đây là thành phần đầu tiên được gọi nên thông thường khi cấu hình dự án chúng ta sẽ sử dụng chúng đầu tiên. Có khả năng đảm nhận những công việc sau:

- Thay đổi, chỉnh sửa `Request Object`, `Response Object`.
- Kết thúc vòng đời của Request hoặc Response.

Cũng được khai báo thêm với `Injectable`

```ts
import {Injectable, NestMiddleware} from "@nestjs/common";
import {Request, Response, NextFunction} from "express";

@Injectable()
export class LoggerMiddleware implements NestMiddleware {
  use(req: Request, res: Response, next: NextFunction) {
    console.log("Request...");
    next();
  }
}

// 関数として定義する
export function logger(req: Request, res: Response, next: NextFunction) {
  console.log(`Request...`);
  next();
}
```

Có 2 cách sử dụng Middleware:

① Sử dụng theo từng module, controller cụ thể.

```ts
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer.apply(LoggerMiddleware).forRoutes(CatsController);
  }
}
```

② Sử dụng cho global module.

Middleware được đăng ký global trên toàn ứng dụng của chúng ta và sẽ được áp dụng cho tất cả các request được gửi đến. Chúng ta thường thấy khi sử dụng các package như cors, helmet, body-parser,... với cú pháp app.use().

Trong ví dụ của chúng ta, mình sẽ sử dụng helmet và một custom middleware để log ra thứ tự:

```ts
import { Logger } from '@nestjs/common';
import { NestFactory } from '@nestjs/core';
import { Request, Response } from 'express';
import helmet from 'helmet';
import { AppModule } from './app.module';

async function bootstrap() {
  const logger = new Logger(bootstrap.name);
  const app = await NestFactory.create(AppModule);
  app.use(helmet());
  app.use((req: Request, res: Response, next) => {
    logger.debug('===GLOBAL MIDDLEWARE===');
    next();
  });
  await app.listen(3000);
}
bootstrap();
```

## Guards

Được định nghĩa với `@Injectable()`, implements `CanActivate` interface

Thường có nhiệm vụ quyết định xem có nên xử lí request không dựa theo (quyền, role, ACL, ...).  Mục đích duy nhất của Guard là xác định xem có cho phép request được xử lý bởi route handler hay không tại run-time. Có thể các bạn sẽ có thắc mắc Guard và Middleware đều xử lý logic tương tự nhau, tuy nhiên về bản chất thì Middleware sau khi gọi hàm next() thì sẽ không biết handler nào sẽ được gọi sau đó. Ngược lại, Guard nhờ vào việc có thể truy cập vào ExcecutionContext instance nên có thể biết được handler nào tiếp theo sẽ được gọi sau khi gọi hàm next(). Việc sử dụng Guard giúp chúng ta đưa logic xử lý vào chu trình của ứng dụng một cách rõ ràng và dễ hiểu. Điều này giúp cho code của chúng ta trở nên ngắn gọn, dễ đọc và dễ bảo trì hơn, đồng thời giúp giảm thiểu các lặp lại trong code (DRY). Từ đó, ứng dụng có thể được phát triển và nâng cấp một cách dễ dàng và hiệu quả hơn.

```ts
@Injectable()
export class AuthGuard implements CanActivate {
  canActivate(
    context: ExecutionContext
  ): boolean | Promise<boolean> | Observable<boolean> {
    const request = context.switchToHttp().getRequest<Request>();
    return validateRequest(request);
}
```

Guard có thể được sử dụng ở method, controller, global levels.

```ts
@Post()
@UseGuards(AuthGuard)
async create(@Body() createCatDto: CreateCatDto) {
  // ...
}
```

```ts
@UseGuards(AuthGuard)
export class CatsController {}
```

```ts
const app = await NestFactory.create(AppModule);
app.useGlobalGuards(AuthGuard);
```
## Interceptors

Được định nghĩa với `@Injectable()`, implements `NestInterceptor` interface.
**Interceptors** thì nó cho phép chúng ta xử lý các request và response trước khi chúng được xử lý bởi controller hoặc được trả về cho client. Vì thế chúng ta có thể chèn thêm custom logic vào quá trình xử lý request/response của ứng dụng. Interceptors thường được sử dụng cho các trường hợp sau đây:

- **Logging**: Ghi lại thông tin request và response để giám sát và phân tích
- **Caching**: Lưu cache của các response để giảm thiểu việc truy vấn database hoặc service bên ngoài
- **Transformation**: Chuyển đổi request hoặc response để phù hợp với định dạng mong muốn
- **Error handling**: Xử lý lỗi và trả về response phù hợp

Vì **Interceptors** xử lý cả request lẫn response nên sẽ có 2 phần:

- **Pre**: trước khi đến method handler của controller
- **Post**: sau khi có response trả về từ method handler.

```ts
@Injectable()
export class LoggingInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    console.log("Before...");

    const now = Date.now();
    return next
      .handle()
      .pipe(tap(() => console.log(`After... ${Date.now() - now}ms`)));
  }
}
```

Nó được sử dụng ở method, controller, global levels

```ts
@Post()
@UseInterceptors(LoggingInterceptor)
async create(@Body() createCatDto: CreateCatDto) {
  // ...
}
```

```ts
@UseInterceptors(LoggingInterceptor)
export class CatsController {}
```

```ts
const app = await NestFactory.create(AppModule);
app.useGlobalInterceptors(LoggingInterceptor);
```


## Exception filters

Thường được định nghĩa với decorator là `Catch()`. Được sử dụng để "bắt" các ngoại lệ không được xử lí, nó sẽ kiểm soát các ngoại lệ (HttpException) khi ngoại lệ được gửi về phía client.

Cơ chế mặc định ở đây đó là nó sẽ tìm ra ngoại lệ, sau đó sẽ chuyển sang dạng HTTP Response.

```ts
@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception: HttpException, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const request = ctx.getRequest<Request>();
    const status = exception.getStatus();

    // Chỉnh sửa lại response
    response.status(status).json({
      statusCode: status,
      timestamp: new Date().toISOString(),
      path: request.url,
    });
  }
}
```

Exception filter có thể được sử dụng cho cả 3 level: method, controller, global với cách thức như sau:

① Method

```ts
@Post()
@UseFilters(HttpExceptionFilter)
async create(@Body() createCatDto: CreateCatDto) {
  // ...
}
```

② Controller

```ts
@UseFilters(HttpExceptionFilter)
export class CatsController {}
```

③ Global

```ts
const app = await NestFactory.create(AppModule);
app.useGlobalFilters(HttpExceptionFilter);
```


## Pipes

Được định nghĩa đi kèm với `@Injectable()`, và implement `PipeTransform` interface. Mục đích chính của việc sử dụng pipe đó là:

- Xác thực dữ liệu: Kiểm tra xem dữ liệu được gửi từ client có đúng định dạng và có hợp lệ hay không.
- Chuyển đổi dữ liệu: Chuyển đổi định dạng dữ liệu được gửi từ client thành dạng dữ liệu mà server có thể hiểu được, hoặc ngược lại chuyển đổi định dạng dữ liệu gửi về cho client.
- Sàng lọc dữ liệu: Lọc bỏ dữ liệu không cần thiết, nhạy cảm hoặc nguy hiểm.

Có tất cả 9 loại pipes:

- ValidationPipe
- ParseIntPipe
- ParseFloatPipe
- ParseBoolPipe
- ParseArrayPipe
- ParseUUIDPipe
- ParseEnumPipe
- DefaultValuePipe
- ParseFilePipe

Ví dụ:

```ts
@Injectable()
export class ParseIntPipe implements PipeTransform<string, number> {
  transform(value: string, metadata: ArgumentMetadata): number {
    const val = parseInt(value, 10);
    if (isNaN(val)) {
      throw new BadRequestException("Validation failed");
    }
    return val;
  }
}
```

Pipe được sử dụng ở cả 4 levels: param, method, controller, global.

```ts
@Get(':id')
async findOne(@Param('id', ParseIntPipe) id) {
  return this.catsService.findOne(id);
}
```

```ts
@Post()
@UsePipes(ValidationPipe)
async create(@Body() createCatDto: CreateCatDto) {
  // ...
}
```

```ts
const app = await NestFactory.create(AppModule);
app.useGlobalPipes(ValidationPipe);
```


## Về repository

Repository trong một project NestJS sẽ đảm nhận nhiệm vụ giao tiếp với DB cũng như tiến hành chỉnh sửa, thêm mới dữ liệu.

Ngoài ra việc giao tiếp với các hệ thống bên ngoài cũng do repository đảm nhận

## Tổng kết

Gom tất cả các thành phần đã kể trên lại, chúng ta có thể phác hoạ ra mô hình tổng quan của một ứng dụng sử dụng NestJS như sau:

![](/assets/img/nestjs/life-cycle.png)

Hi vọng bài viết sẽ đem lại cho bạn đọc một cái nhìn tổng quan nhất về một ứng dụng NestJS.

Cám ơn bạn đọc !

# [References]
- <https://zenn.dev/morinokami/articles/nestjs-overview>
- <https://medium.com/aws-tip/understanding-nestjs-architecture-f257d054211d>
