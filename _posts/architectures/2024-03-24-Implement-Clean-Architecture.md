---
title: "[Architecture] Implement Clean Architecture"
author: thuohuynh
date: 2024-03-24 12:00:00 +0900
categories: [Architecture, Clean Architecture]
tags: [architecture]
render_with_liquid: false
---

## Mục lục

1. [Phần 1: Clean Architecture](/posts/Clean-Architecture)
2. [Phần 2: Implement Clean Architecture](/posts/Implement-Clean-Architecture)

# Phần 2: Implement Clean Architecture

Trong bài viết này mình sẽ cung cấp một ví dụ cụ thể cho việc vận dụng nguyên tắc và tư duy Clean Architecture cho một service REST API viết bằng ExpressJS và Typescript.

> Notes:
> Trên thực tế, các Express service sẽ không nhất thiết phải sử dụng bất kỳ một kiến trúc nào cả. Bài viết này cũng chỉ là một ví dụ và không phải một best practice.

## Các vấn đề hiện tại của source code TODO List

Service TODO List bao gồm 5 REST API cơ bản cho CRUD (Creat-Read-Update-Delete), tương đương với 5 nghiệp vụ (5 business logic). Tất cả đặt hết trong một file `main.ts`

Chúng ta dễ dàng nhìn ra được:

- Không có sự phân chia và tổ chức source code, không sử dụng các package hỗ trợ.
- Mỗi handler method (hàm xử lý HTTP request) là một khối code đảm trách từ nhận các data từ request, kiểm tra tính hợp lệ, thao tác với DB và trả về client với JSON Format.

Hệ quả là:

- Khi muốn thay đổi logic hoặc DB chẳng hạn thì buộc phải viết lại cả handler.
- Rủi ro bị conflict code khi team work là rất cao vì code ở một file duy nhất.
- Không thể hiện Unit Test các logic của nghiệp vụ, cách duy nhất là phải run Postgres và service lên rồi thực hiện các HTTP Request để kiểm tra. Việc này gây lãng phí thời gian và tài nguyên.

## Ứng dụng các nguyên lý của Clean Architecture vào service Express

### Thiết kế các tầng phù hợp

# [References]

- <https://200lab.io/blog/clean-architecture-uu-nhuoc-va-cach-dung-hop-ly/>
- <https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html>
- <https://www.youtube.com/watch?v=QR2PScc_fMA&t=2s>
