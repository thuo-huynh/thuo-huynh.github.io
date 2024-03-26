---
title: "[Nginx] Phần 2: Kiến thức cơ bản"
author: thuohuynh
date: 2024-03-11 00:00:00 +0900
categories: [WebServer, Nginx]
tags: [nginx]
render_with_liquid: false
---

<!-- ## Cân bằng tải

Nginx có thể được sử dụng như một load balancer với hai nhiệm vụ như sau:

**Điều phối requests tới các servers:**

![File_000]()

**Phân tải khi có server gặp sự cố:**

![File_000 (1)]()

## Chiến lược cân bằng tải

Để thể điều phối được request cho server thích hợp, load balancing sử dụng một trong những thuật toán sau:

- Round Robin: Khi sử dụng thuật toán này, các request sẽ được phân phối tuần tự cho 1 nhóm server.
- Weighted Round Robin: Dựa trên thuật toán Round Robin, người quản trị mạng sẽ dựa vào khả năng xử lí request của từng server và đánh thứ độ ưu tiên - cho các server, request sẽ được gửi tới server theo độ ưu tiên của chúng.
- Least Connections: Request sẽ được gửi tới server có ít kết nối nhất với client để xử lí.
- Weighted Least Connections: Request sẽ được gửi tới server có tốc độ phản hồi response cao nhất và ít kết nối nhất.
- IP Hash: Địa chỉ IP của client sẽ được sử dụng để nhận biết server nào sẽ nhận được request từ người dùng.

### **Round Robin**
Như ở hình trên bạn thấy, khi server 1 gặp sự cố thì toàn bộ các request từ phía client sẽ được chuyển tiếp tới cho 2 server còn lại là `server 2` và `server 3`.

Các bạn có thể tham khảo code demo của mình ở đây <https://github.com/>

Phân tích một chút về môi trường. Ở đây mình dựng nên 3 servers bằng `express JS` lần lượt lắng nghe ở các cổng `1111`, `2222`, `3333`.

**Server 1:**

```JS
app.listen(1111, () => {
  console.log('Listening on port 1111');
});
```

**Server 2:**

```JS
app.listen(2222, () => {
  console.log('Listening on port 2222');
});
```

**Server 3:**

```JS
app.listen(3333, () => {
  console.log('Listening on port 3333');
});
```

Load balancer sẽ lắng nghe ở cổng `8888`

```nginx
events {}

http {
  upstream express_servers {
    server localhost:1111;
    server localhost:2222;
    server localhost:3333;
  }

  server {s
    listen 8888;
         
    location / {
      proxy_pass http://express_servers;
    }
  }
}
```

Ở phần config cho nginx trên, mình sử dụng `upstream` (<http://nginx.org/en/docs/http/ngx_http_upstream_module.html>), context này giúp định nghĩa một nhóm các servers sẽ được tham chiếu sau đó bởi proxy, ...

Ở phần config cho proxy (lưu ý ở đây là cách config load balancer và proxy không khác nhau quá nhiều) mình thêm directive
`proxy_pass http://express_servers;` có nghĩa là các req đến địa chỉ `localhost:8888/` sẽ được chuyển tiếp đến `stream server express_servers` như định nghĩa ở trên.

Ở đây các requests sẽ lần lượt được luân chuyển đến các servers `localhost:1111`, `localhost:2222`, `localhost:3333` được chạy bằng express ở trên. Thuật toán luân chuyển request mặc định sử dụng ở đây là `Round robin`.

Nói sơ qua thì đây là thuật toán luân chuyển requests đến các servers dựa theo trọng số (server weight) tương ứng với từng server. Weight càng cao thì tần suất nhận được request càng cao và ngược lại.
Mặc định thì weight của các servers là bằng nhau.

Để "xác minh" xem `load balancer` có hoạt động như ý muốn của chúng ta hãy không bạn hãy thử chạy lệnh sau trong terminal của mình

```shell
while sleep 1; do curl http://localhost:8888; done
```

Câu lệnh trên sẽ đều đặn thực hiện lệnh `curl http://localhost:8888` đến proxy của chúng ta cứ sau một giây một.
Và đây là kết quả chúng ta nhận được

![Screen Shot 2022-09-23 at 16 21 25](https://user-images.githubusercontent.com/15076665/191910278-62a2fe09-8940-49d1-9d0e-c660d375e2d0.png)

Các requests lần lượt được luân chuyển đến các server theo thứ tự 1, 2, 3 -->