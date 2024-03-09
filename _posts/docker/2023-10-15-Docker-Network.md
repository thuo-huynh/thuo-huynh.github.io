---
title: "[Docker] Network"
author: thuohuynh
date: 2023-10-10 16:10:00 +0900
categories: [Docker, Network]
tags: [docker]
render_with_liquid: false
---

# Docker Networking
In the diagram above, developers use Docker to simplify managing application requirements and dependencies. They create a Docker File, which generates Docker Images containing all the necessary dependencies for an application.

Docker Containers are the running instances of these Docker Images. These images can be stored on the Docker Hub, a Git repository for Docker Images, with both public and private repositories available.

You can download images from public repositories or upload your own to the Docker Hub. Teams like Quality Assurance and Production then fetch these images and create individual containers. These containers interact over a network to execute tasks, which is the essence of Docker Networking.


Docker networking enables container-to-container and external system communication. It offers isolation, security, and scalability for applications. Docker's pluggable architecture allows you to select the most suitable networking driver for your specific requirements. It includes various built-in networking drivers to choose from. There are mainly 5 network drivers: Bridge, Host, None, Overlay, Macvlan

- Bridge: The default driver that creates a private network for containers on the host.
- Host: Bypasses the Docker network stack and uses the host’s network directly.
- Overlay: Creates a network spanning across multiple Docker hosts, useful for Docker Swarm mode.
- Macvlan: Assigns a MAC address to a container and connects it directly to the physical network.

# Container Network Model(CNM)
