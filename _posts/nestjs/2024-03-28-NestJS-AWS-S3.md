---
title: "[NestJS] Upload files to AWS S3 using NestJS"
author: thuohuynh
date: 2024-03-28 00:00:00 +0900
categories: [NestJS]
tags: [nestjs]
render_with_liquid: false
---

Trong bài viết này, mình sẽ hướng dẫn mọi người tải file lên AWS S3 trong NestJS.

### Đầu tiên mọi người tạo project theo command

```bash
nest new nest-fileupload
```

### Sau đó chúng ta sẽ tạo một bucket trên AWS S3, phần này mọi người tự tiến hành tạo nhé

- Bucket: nestjs-fileupload
- Region: ap-northeast-1

### Sau khi đã tạo project xong, chúng ta tạo file `.env` để thiết lập biến môi trường

Lấy `AWS_ACCESS_KEY_ID` và `AWS_SECRET_ACCESS_KEY` từ IAM và thiết lập chúng thành biến môi trường trong tệp `.env`.

```dotenv
AWS_REGION=ap-northeast-1
AWS_ACCESS_KEY_ID=accessKey
AWS_SECRET_ACCESS_KEY=secretKey
AWS_BUCKET_NAME='nestjs-fileupload'
```

### Tiếp đến chúng ta sẽ tạo module upload

Tạo end point CRUD thông qua Nest CLI.

```bash
nest g res file-upload
```

### Cài đặt các thư viện cần thiết

Cài đặt các thư viện sẽ được sử dụng trong project này.

```bash
npm i @nestjs/config aws-sdk @types/aws-sdk uuid
npm i -D @types/multer @types/uuid
```

### Tạo `ConfigService`

Chúng ta sẽ tạo một `service` để đọc các biến môi trường.

Import `ConfigModule` vào trong `app.module.ts`.

```ts
// app.module.ts
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { FileUploadModule } from './file-upload/file-upload.module';
import { ConfigModule } from '@nestjs/config';
import configuration from './config/configuration';

@Module({
  imports: [
    ConfigModule.forRoot({
      load: [configuration],
      isGlobal: true,
    }),
    FileUploadModule,
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

Sau đó tạo config giống như bên dưới nhé, config này sẽ đọc data từ file `.env`.

```typescript
// src/config/configuration.ts
export default () => ({
  aws: {
    region: process.env.AWS_REGION,
    accessKey: process.env.AWS_ACCESS_KEY_ID,
    secretKey: process.env.AWS_SECRET_ACCESS_KEY,
    s3BucketName: process.env.AWS_BUCKET_NAME,
  },
});
```
Sau đó setup AWS config vào trong file main.ts

```ts
// main.ts
import { config } from 'aws-sdk';
import { ConfigService } from '@nestjs/config';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  const configService = app.get(ConfigService);
  config.update({
    accessKeyId: configService.get('aws.accessKey'),
    secretAccessKey: configService.get('aws.secretKey'),
    region: configService.get('aws.region'),
  });
  await app.listen(3000);
}
bootstrap();
```

Để đọc các thiết lập từ `ConfigService`, import `ConfigModule` vào `file-upload.module.ts`.

```typescript
// src/file-upload/file-upload.module.ts
import { Module } from '@nestjs/common';
import { FileUploadService } from './file-upload.service';
import { FileUploadController } from './file-upload.controller';
import { ConfigModule } from '@nestjs/config';

@Module({
  imports: [ConfigModule],
  controllers: [FileUploadController],
  providers: [FileUploadService],
})
export class FileUploadModule {}
```

### Tạo `service` để upload file

Service này sẽ nhận file từ Controller, sau đó upload lên AWS S3.

```typescript
// src/file-upload/file-upload.service.ts
import { Injectable } from "@nestjs/common";
import { ConfigService } from "@nestjs/config";
import { S3 } from "aws-sdk";
import { v4 as uuid } from "uuid";

@Injectable()
export class FileUploadService {
  constructor(private readonly configService: ConfigService) {}
  async uploadFile(dataBuffer: Buffer, filename: string) {
    const s3 = new S3();
    const uploadResult = await s3
      .upload({
        Bucket: this.configService.get("aws.s3BucketName"),
        Body: dataBuffer,
        Key: `${uuid()}-${filename}`,
      })
      .promise();

    console.log("Key:", uploadResult.Key);
    console.log("url:", uploadResult.Location);
  }
}
```

Sau khi upload file xong, service này sẽ `console.log` ra key và location đã upload.

### Tạo `FileUploadController` để nhận file từ người dùng

```typescript
// src/file-upload/file-upload.controller.ts
import {
  Controller,
  Post,
  UseInterceptors,
  Req,
  UploadedFile,
} from '@nestjs/common';
import { FileUploadService } from './file-upload.service';
import { FileInterceptor } from '@nestjs/platform-express';
import { response } from 'express';

@Controller('files')
export class FileUploadController {
  constructor(private readonly fileUploadService: FileUploadService) {}

  @Post('upload')
  @UseInterceptors(FileInterceptor('file'))
  async uploadFile(@Req() request, @UploadedFile() file: Express.Multer.File) {

    try {
      await this.fileUploadService.uploadFile(file.buffer, file.originalname);
    } catch (error) {
      return response
        .status(500)
        .json(`Failed to upload image file: ${error.message}`);
    }
  }
}
```

### Thử upload file bằng Postman

Đến đây mình sẽ thử dùng Postman để tải 1 file hình ảnh lên trên AWS S3 mà mình đã setup.

Ở phần `body`, mình sẽ chọn `form-data`. Sau đó nhập `key=file`, và lựa chọn file sau đó kéo vào chỗ `value`

```
POST http://localhost:3000/files/upload
```

Các key và URL của tệp được lưu trữ trên S3 sẽ được hiển thị trên console.

Dưới đây là những gì hiển thị trên cửa sổ console:

```console
Key: 7e81d580-b519-4a6e-a945-b4cdf1e10df1-imadamio.jpg
url: https://xxxxxx.s3.ap-northeast-1.amazonaws.com/7e81d580-b519-4a6e-a945-b4cdf1e10df1-imadamio.jpg
```

Khi kiểm tra trong bucket của S3, ta có thể thấy rằng các tệp đã được lưu trong bucket như mong đợi.

### Lưu nhiều tệp vào S3

Để lưu nhiều tệp vào S3, chúng ta sẽ điều chỉnh như sau:

### Tạo Interceptor

Chúng ta sẽ tạo một interceptor để kiểm tra xem chỉ có tệp extentiopn là hình ảnh thì mới cho phép upload.

```typescript
import { Request } from 'express';
import { BadRequestException } from '@nestjs/common';

export const imageFileFilter = (
  req: Request,
  file: {
    fieldname: string;
    originalname: string;
    encoding: string;
    mimetype: string;
    size: number;
    destination: string;
    filename: string;
    path: string;
    buffer: Buffer;
  },
  callback: (error: Error | null, acceptFile: boolean) => void,
) => {
  if (!file.originalname.match(/\.(jpg|jpeg|png|webp|gif|avif)$/)) {
    return callback(
      new BadRequestException('Chỉ chấp nhận ảnh, bạn nhé!'),
      false,
    );
  }
  callback(null, true);
};
```

### Tạo method để tải nhiều tệp lên S3

Chúng ta sẽ thêm một method có tên là `uploadFiles`.

```typescript
import { Injectable } from '@nestjs/common';
import { ConfigService } from '@nestjs/config';
import { S3 } from 'aws-sdk';
import { v4 as uuid } from 'uuid';
import { PublicFile } from './dto/public-file';

@Injectable()
export class FileUploadService {
  constructor(private readonly configService: ConfigService) {}
  async uploadFile(dataBuffer: Buffer, filename: string) {
    const s3 = new S3();
    const uploadResult = await s3
      .upload({
        Bucket: this.configService.get('aws.s3BucketName'),
        Body: dataBuffer,
        Key: `${uuid()}-${filename}`,
      })
      .promise();

    console.log('Key:', uploadResult.Key);
    console.log('url:', uploadResult.Location);
  }

  async uploadFiles(files: Array<Express.Multer.File>): Promise<PublicFile[]> {
    const s3 = new S3();
    const publicFiles: PublicFile[] = [];
    for (const file of files) {
      const uploadResult = await s3
        .upload({
          Bucket: this.configService.get('aws.s3BucketName'),
          Body: file.buffer,
          Key: `${uuid()}-${file.originalname}`,
        })
        .promise();
      console.log(`${file.originalname} - Key: ${uploadResult.Key}`);
      console.log(`${file.originalname} - Location: ${uploadResult.Location}`);
      publicFiles.push({
        originalname: file.originalname,
        key: uploadResult.Key,
        location: uploadResult.Location,
      });
    }
    return publicFiles;
  }
}
```

### Tạo Controller để nhận nhiều tệp

Chúng ta sẽ thêm một phương thức trong Controller có tên là `uploadFiles`.

```typescript
import {
  Controller,
  Post,
  UseInterceptors,
  Req,
  UploadedFile,
  UploadedFiles,
} from '@nestjs/common';
import { FileUploadService } from './file-upload.service';
import {
  FileFieldsInterceptor,
  FileInterceptor,
} from '@nestjs/platform-express';
import { response } from 'express';
import { imageFileFilter } from './interceptors';
import { PublicFile } from './dto/public-file';

@Controller('files')
export class FileUploadController {
  constructor(private readonly fileUploadService: FileUploadService) {}

  @Post('upload')
  @UseInterceptors(FileInterceptor('file'))
  async uploadFile(@Req() request, @UploadedFile() file: Express.Multer.File) {
    console.log('uploadFile is called', file);
    try {
      await this.fileUploadService.uploadFile(file.buffer, file.originalname);
    } catch (error) {
      return response
        .status(500)
        .json(`Failed to upload image file: ${error.message}`);
    }
  }

  @Post('uploads')
  @UseInterceptors(
    FileFieldsInterceptor([{ name: 'files', maxCount: 4 }], {
      fileFilter: imageFileFilter,
      limits: { fileSize: 1024 * 1024 * 4 },
    }),
  )
  async uploadFiles(
    @UploadedFiles()
    params: {
      files: Array<Express.Multer.File>;
    },
  ): Promise<PublicFile[]> {
    return await this.fileUploadService.uploadFiles(params.files);
  }
}
```

### Kết quả upload

Mình sẽ thử bỏ thêm nhiều file vào trong value của Postman và gửi đi.

Khi gửi request, AWS S3 sẽ lưu nhiều tệp và trả về một phản hồi giống như sau:

```json
[
  {
    "originalname": "sample1.jpeg",
    "key": "e3bce52f-ede4-441d-84f0-e48277a8b6bc-sample1.jpeg",
    "location": "https://xxx.s3.ap-northeast-1.amazonaws.com/e3bce52f-ede4-441d-dsds-e48277a8b6bc-sample1.jpeg"
  },
  {
    "originalname": "sample2.jpeg",
    "key": "3abde8f6-dd87-4cf7-bc4a-9022d2c894fe-sample2.jpeg",
    "location": "https://xxx.s3.ap-northeast-1.amazonaws.com/3abde8f6-dd87-4cf7-dscsd-9022d2c894fe-sample2.jpeg"
  },
  {
    "originalname": "sample3.jpeg",
    "key": "af3760c4-3b5a-4c72-8185-abf32afa6e3c-sample3.jpeg",
    "location": "https://xxx.s3.ap-northeast-1.amazonaws.com/af3760c4-3b5a-4c72-cds2-abf32afa6e3c-sample3.jpeg"
  }
]
```


# [References]
- <https://github.com/thuo-huynh/nest-aws>

