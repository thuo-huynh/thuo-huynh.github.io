---
title: Docker Network Bridge
author: thuohuynh
date: 2023-10-10 16:10:00 +0900
categories: [Docker, Network, Bridge]
tags: [docker]
render_with_liquid: false
---

# Kiến trúc của Docker

## Kiến trúc Client-Server của Docker

Docker sử dụng mô hình kiến trúc Client-Server, bao gồm các thành phần chính sau:

- **Docker Daemon:** Chạy trên host, đóng vai trò server, nhận RESTful requests từ Docker Client và thực thi.
- **Docker CLI:** Là client, cung cấp giao diện tương tác với người dùng (command line) và gửi RESTful requests đến Docker Daemon.
- **Docker Registry:** Private hoặc public registry, nơi lưu trữ và chia sẻ các Docker Image.

Docker Daemon, hay Docker Engine, là một runtime nhẹ giúp xây dựng, chạy và quản lý các containers và các thành phần liên quan.

## Các đối tượng quản lý của Docker Engine

Docker Engine quản lý 4 đối tượng chính, mỗi đối tượng có tên và ID:

- **Image:** Là template chỉ đọc được để tạo containers, cấu trúc thành layers và toàn bộ layers đều chỉ đọc được.
- **Container:** Là một instance chạy của Docker Image, tạo writable layer khi chạy. Thay đổi dữ liệu chỉ xảy ra ở writable layer. Có thể tạo Image mới từ Container bằng lệnh commit.
- **Network:** Cung cấp private network (VLAN) cho việc liên lạc giữa các container trên một host hoặc giữa các container trên nhiều hosts (multi-host networking).
- **Volume:** Sử dụng để lưu trữ dữ liệu độc lập với vòng đời của container, giúp chia sẻ dữ liệu giữa các containers và với host.

![Mối quan hệ của Docker Image, Container, Network, Volume](link_to_image)

## Một số câu lệnh cơ bản của Docker

### Image

- `docker image pull <image_name>`: Pull một image từ Docker Hub.
- `docker image ls`: Liệt kê các images hiện có.
- `docker image rm <image_name>`: Xóa một image.
- `docker image build -t <image_name> .`: Build một image từ Dockerfile.
- `docker image history <image_name>`: Xem lịch sử build của một image.

### Container

- `docker container run <image_name>`: Tạo một container.
- `docker container start <container_name>`: Start một container.
- `docker container stop <container_name>`: Stop một container.
- `docker container rm <container_name>`: Xóa một container.
- `docker container ps`: Liệt kê các container đang chạy.
- `docker container diff <container_name>`: Xem các thay đổi trên container.

## Docker Container

### Vòng đời của Container

Docker Container có đặc điểm là chóng tàn (ephemeral), khởi tạo và xóa nhanh chóng. Không nên lưu dữ liệu trong container, thay vào đó nên sử dụng Volume.

Container sử dụng cơ chế process-based. Người dùng có thể tạo Docker Container bằng cách sử dụng các lệnh như:

- `docker run <image_name> <command>`
- `docker run <image_name>`

Nếu process với PID = 1 kết thúc, Docker Container cũng kết thúc, và tất cả các process con của PID = 1 sẽ bị kill.

### Docker Image

Để chạy ứng dụng với Docker, cần đóng gói ứng dụng vào Docker Image. Một số điểm cần lưu ý:

- Sử dụng entrypoint để quản lý việc start và stop ứng dụng.
- Sử dụng shell để làm entrypoint và xử lý các command được truyền vào khi chạy Docker Container.
- Sử dụng Python Jinja2 để sinh ra các file cấu hình từ template.
- Copy file cấu hình bằng lệnh `cp`.
- Thực hiện các lệnh để khởi tạo ứng dụng, chown quyền các file cần thiết để đảm bảo ứng dụng có thể start được.
- Đối với việc tắt ứng dụng, đảm bảo ứng dụng xử lý đúng SIGTERM bằng shell script hoặc handle SIGTERM trong ứng dụng.
