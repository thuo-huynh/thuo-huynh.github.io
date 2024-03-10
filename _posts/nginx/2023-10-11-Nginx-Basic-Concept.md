---
title: "[Nginx] Basic concept."
author: thuohuynh
date: 2023-10-11 16:10:00 +0900
categories: [WebServer, Nginx]
tags: [nginx]
render_with_liquid: false
---

# Các khái niệm cơ bản của Nginx

Nginx là một công cụ quen thuộc với những người làm server (backend), và việc hiểu về các khái niệm cơ bản của nó là rất quan trọng. Trong bài viết này, tôi sẽ chia sẻ những ghi chú cá nhân từ quá trình tìm hiểu về Nginx.

Hai nguồn chính tôi sử dụng để học về Nginx là:

1. [YouTube - Nginx Tutorial](https://www.youtube.com/watch?v=7VAI73roXaY)
2. [Udemy - Nginx Fundamentals](https://www.udemy.com/course/nginx-fundamentals/)

## Nginx là gì?

Nginx không chỉ là một server cung cấp nội dung cho trang web mà còn có thể được sử dụng như một reverse proxy và HTTP cache. Hình minh họa dưới đây mô tả khả năng của Nginx:

![Nginx Diagram](/assets/img/nginx/nginx-diagram.webp)

Ngoài ra, khi có nhiều servers, Nginx có thể hoạt động như một load balancer để phân phối các requests đến các servers tương ứng.

## Cài đặt Nginx

Thông tin chi tiết về cách cài đặt Nginx có thể được tìm thấy tại [đây](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/).

## Configuration Terms

Có hai khái niệm cấu hình cơ bản trong Nginx:

- **Context:** Trong Nginx, context tương đương với phạm vi hoạt động, xác định nơi áp dụng cấu hình, như `http` là toàn bộ trang web, `server` là một máy chủ cụ thể, và `location` là định danh của một tài nguyên trên server.

- **Directive:** Là các hướng dẫn được sử dụng để định cấu hình trong Nginx, như `listen`, `root`, `return`. Mỗi directive có một tác dụng cụ thể trong quá trình xử lý request và response.

Ví dụ:

```nginx
http {
  server {
    listen 8080;
    root path_to_mysite/; # Directive, giống như một config
  }
}
```

Có ba context quan trọng cần chú ý trong Nginx: `http`, `server`, và `location`.

1. **`http`**: Định cấu hình ở mức toàn bộ server, chứa các cài đặt chung cho toàn bộ trang web.
2. **`server`**: Định cấu hình cho một server cụ thể, bao gồm cài đặt như cổng lắng nghe và SSL.
3. **`location`**: Xác định cấu hình cho một URI hoặc nhóm URI cụ thể, quyết định cách xử lý request tới server.

## File Cấu Hình Nginx

Cấu hình thường được đặt trong file `nginx.conf`, mặc định là `/usr/local/etc/nginx/nginx.conf`. Tuy nhiên, bạn có thể sử dụng file cấu hình cá nhân của mình bằng cách sử dụng lệnh sau:

```bash
nginx -c file_path
```

## Reload Nginx

Có sự khác biệt giữa `nginx reload` và `nginx restart`:

- `nginx -s reload` reload lại config của nginx mà **không tắt service**, nếu có vấn đề với cấu hình, toàn bộ tiến trình có thể dừng lại.
- `nginx restart` **tắt hẳn nginx** đi và khởi động lại service, nếu có vấn đề với cấu hình, service sẽ dừng lại.

## Serve Static Content

Đối với nội dung tĩnh như HTML hoặc CSS, bạn cần định nghĩa các loại như sau:

```nginx
http {
  types {
    text/html html;
    text/css css;
  }
}
```

Các types này có thể được tham khảo ở file mime.types trong hệ thống của bạn

Lấy ví dụ với css, nếu ta không có directive text/css css; thì khi load file .css về thì trình duyệt sẽ hiểu nó là file text/plain như hình bên dưới, lúc này các style bên trong file css sẽ không được apply cho trang HTML của bạn. Còn trong config của mình có directive text/css css; thì trình duyệt sẽ hiểu đó là file text/css, khi đó các style sẽ được áp dụng cho trang HTML của bạn.

## Blocks Location trong Nginx

Có ba cách để cấu hình cho URI trong Nginx: Regex match, Prefix match, và Exact match.

- Regex match: 

```nginx
# REGEX match - case sensitive
location ~ /greet[0-9] {
  return 200 'Hello from NGINX "/greet" location - REGEX MATCH.';
}
```
~ symbol cho thấy bạn sẽ sử dụng regex ở đây, tuy nhiên mặc định thì đây là case sensitive (phân biệt hoa thường), còn nếu thêm * symbol thì sẽ là case insensitive (không phân biệt hoa thường)

```nginx
# REGEX match - case insensitive
location ~* /greet[0-9] {
    return 200 'Hello from NGINX "/greet" location - REGEX MATCH INSENSITIVE.';
}
```

- Prefix match:
Prefix match nếu bạn thiết lập URI = test thì các URIs khác ví dụ như:
    - /greeting
    - /greeted

- Exact match:
Exact match bạn thêm dấu = phía trước URI như sau:

```nginx
# Exact match
location = /greet {
return 200 'Hello from NGINX "/greet" location - EXACT MATCH.';
}
```

Lúc này config chỉ matching URI = "/greet" mà thôi, các URIs như "/greeting", "/greeted", ... sẽ không được matching.

Xét về độ ưu tiên thì **Regex match có độ ưu tiên cao hơn Prefix match**. **Tuy nhiên** nếu bạn sử dụng **^~ Preferential match thì Preferential Prefix match lúc này sẽ có độ ưu tiên cao hơn Regex match**

```nginx
# Preferential Prefix match
location ^~ /Greet2 {
    return 200 'Hello from NGINX "/greet" location.';
}
```
## Redirect và Rewrite

### Redirect:

```nginx
location /old-uri {
  return 307 /new-uri;
}
```
Ở đây 307 chính là HTTP Code của redirect request

### Rewrite:
Trong trường hợp bạn không muốn bị redirect đến một trang khác, vẫn giữ nguyên URL như cũ nhưng chỉ khác nội dung thì rewrite là một giải pháp

```nginx
rewrite ^/number/(\w+) /count/$1;
```

Ở ví dụ trên bạn thấy (\w+) được bao bởi một cặp ngoặc đơn (), việc này giúp bạn có thể sử dụng giá trị của nó ở $1. Ví dụ nếu URI là /number/100 thì giá trị của $1 sẽ là 100, lúc này chúng ta sẽ được rewrite sang /count/100

> Note: 
Chú ý là rewrite có mức độ ưu tiên cao hơn location
{: .prompt-info }

## Variables trong Nginx

Trong Nginx có hai loại biến chính: Configuration variables và Nginx Module variables.
- Configuration variables (đây là các biến mà bạn sẽ tự định nghĩa): set $var 'something';
- NGINX Module variables (đây là các biến có sẵn, do nginx module định nghĩa): $http, $uri, $args - query string params

```nginx
set $isthursday 'No';

if ($date_local ~ 'Thursday') {
  set $isthursday 'Yes';
}

location /inspect {
  return 200 "Is Thursday: $isthursday";
}
```

Trong ví dụ trên, ta kiểm tra xem biến đã định nghĩa sẵn của Nginx là $date_local (ngày hiện tại) có phải là thứ Năm hay không. Nếu đúng, biến $isthursday sẽ được gán giá trị "Yes" (ban đầu là "No"). Trong Nginx, để nhúng giá trị của biến vào một chuỗi, sử dụng kí tự $ phía trước tên biến, như trong ví dụ với biến $isthursday.

## Ghi Log

Nginx có hai loại log chính: access log và error log.

```nginx
location /URI {
  access_log log_file_path;
}
```

Bằng cách sử dụng điều này, bạn có thể chỉ định file chứa access log riêng biệt.

Đây chỉ là một số điểm cơ bản trong việc tìm hiểu về Nginx. Trong các phần tiếp theo, chúng ta sẽ khám phá thêm về các chủ đề khác và các tính năng chi tiết của Nginx như Forward Proxy và Reverse Proxy.