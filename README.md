# Docker Guide

For English version, click [here](https://github.com/RAninymous/Docker-Guide/blob/main/Docker%20Guide.md).

## 为什么在团队合作中要使用 Docker？

在本地开发过程中，我们常见的开发流程是这样的：

![image](https://github.com/user-attachments/assets/2b3c5b44-d711-4968-966f-0958cd960b7c)

我们在**本地环境**修改**代码**，通过运行产生的**进程**来查看效果或调试问题。

然而在团队协作中，不同成员的电脑操作系统、Python 版本、依赖库等本地环境常常不一致，这很容易导致：

> “我这边能跑，你那边报错了？”

为了避免这种环境不一致的问题，我们引入 Docker —— 一个可以**封装运行环境**的工具。

通过 Docker，我们可以像下图一样，在每个人的电脑上运行一模一样的环境和代码：

![image](https://github.com/user-attachments/assets/7acffeab-04d4-4fa3-bf6a-80f5a98231a6)

你只需要编写一个简单的 **Dockerfile** 来声明程序运行所需的环境和依赖，Docker 就会为你构建出一个轻量级的、可重复使用的运行环境（即**镜像**）。

至此，你就可以放心地与他人共享、协同开发代码，无需再担心环境配置问题。

---

## 一、个人项目中使用 Docker

### 原理简述

> 将“本地运行环境”换成“容器运行环境”，通过 Docker 统一开发、测试、运行的所有流程。

### 实践操作

假设你的项目结构如下：

```bash
my-app/
├── app/
│   └── main.py
├── requirements.txt
└── Dockerfile
```

接下来我们编写一个 `Dockerfile`：

```Dockerfile
# 使用官方 Python 镜像作为基础
FROM python:3.11

# 设置容器内的工作目录
WORKDIR /app

# 将本地项目文件复制进容器
COPY . .

# 安装依赖
RUN pip install -r requirements.txt

# 容器启动时执行的命令
CMD ["python", "app/main.py"]
```

详细语法参考：[Dockerfile 官方文档](https://docs.docker.com/reference/dockerfile/)

---

### 构建并运行

#### 第一步：构建镜像（build image）

```powershell
docker build -t <image-name> .
```

#### 第二步：运行容器（run container）

```powershell
docker run --name <container-name> <image-name>
```

容器运行后，你的程序就会开始执行。

---

### 重复运行 / 更新

- 以后可以使用以下命令来重新启动容器：

```powershell
docker start <container-name>
```

这相当于重新运行你的应用。

- 只有当你**更改了依赖**（如 Dockerfile或requirements.txt 变了）时，才需要重新构建镜像：

```powershell
docker build -t <image-name> .
```

---

## 二、前后端项目使用 Docker Compose

当你拥有前后端两个服务时，如果希望同时管理它们的运行，可以使用 `docker-compose`。

### 示例项目结构

```bash
my-project/
├── frontend/
│   ├── Dockerfile
│   └── app/
├── backend/
│   ├── Dockerfile
│   └── src/
└── docker-compose.yml
```

### 示例 docker-compose.yml 配置

```yaml
version: '3'
services:
  frontend:
    build:
      context: ./frontend
    ports:
      - "3000:3000"
    volumes:
      - ./frontend:/app  # 映射本地目录以支持热更新
    networks:
      - app-net

  backend:
    build:
      context: ./backend
    ports:
      - "8000:8000"
    volumes:
      - ./backend:/app
    networks:
      - app-net

networks:
  app-net:
```

更多语法见：[Compose File 官方文档](https://docs.docker.com/reference/compose-file/)

---

### 使用流程

#### 首次构建并启动服务

```powershell
docker-compose up --build
```

这一步会读取每个子目录下的 `Dockerfile`，构建镜像并启动容器。

#### 后续启动（无依赖变更时）

```powershell
docker-compose up
```

#### 停止所有容器

```powershell
docker-compose down
```

---

### 前后端如何通信？

由于前后端服务被放置在同一个自定义网络中（`app-net`），它们之间可以通过服务名互相访问。例如：

- 前端请求后端接口时，可以使用地址 `http://backend:8000`，而不是 `localhost`。
- 这是因为 Docker 内部的 DNS 会将 `backend` 解析为对应容器的 IP 地址。

---

## 小结

通过本文你学习了：

- 如何为个人项目编写 Dockerfile；
- 如何构建和运行镜像与容器；
- 如何使用 Docker Compose 同时管理前后端服务；
- 如何在团队中统一开发环境，提高协作效率。

如果你想进一步学习：

- 可以了解 `docker volume` 持久化数据；
- 学习 `docker exec` 进入容器调试；
- 探索如何将镜像上传到 DockerHub；
- 或者迈向更高级的容器编排工具：Kubernetes

---

如果你觉得这份指南对你有帮助，欢迎分享给你的队友一起用 🫶！




