# Docker-Guide

为什么要使用docker？——为了团队合作。那如何使用docker？

## 个人项目

### 原理讲解

我们在个人开发时，常见的场景是如下图这样的：

![image](https://github.com/user-attachments/assets/2b3c5b44-d711-4968-966f-0958cd960b7c)

我们在**本地环境**修改**代码**，通过**运行**出来的**进程**来查看成果/检查问题。

而这有什么问题？当你在团队合作的过程中，不同人电脑上的本地环境是不同的！

而这就需要引入docker了。docker的运行场景是类似下图的（注意这里的code是相同的）：

![image](https://github.com/user-attachments/assets/7acffeab-04d4-4fa3-bf6a-80f5a98231a6)

你只需要添加一个**Dockerfile**，告诉docker你的程序所运行的环境需要什么依赖项，docker就会自动为你生成你的程序运行所需的环境（即**镜像**）。（这里的环境是比虚拟机要更加轻量化的存在。）

至此，你便可以无所顾忌地跟他人分享/合作编辑你的代码了，并且避免“在我的电脑上能跑啊？”的问题。

### 实践操作

在我们现有项目的基础上，我们首先需要创建一个Dockerfile。（如果你不需要担任项目的docker初始化，你便不需要进行这项操作。）

假设我们现在的项目结构是这样的：

```css
my-app/
├── app/
│   └── main.py
├── requirements.txt
└── Dockerfile
```

那么一个Dockerfile的样例便是如此：

```Dockerfile
# 使用官方 Python 镜像作为基础
FROM python:3.11

# 设置工作目录
WORKDIR /app

# 拷贝项目文件到容器内
COPY . .

# 安装依赖
RUN pip install -r requirements.txt

# 启动命令
CMD ["python", "app/main.py"]
```

更详细的Dockerfile创建教程，详见[docker官方文档](https://docs.docker.com/reference/dockerfile/)。

然后使用以下指令来创建镜像：
```
docker build -t <image-name> .
```

创建之后使用以下指令来创建容器：
```
docker run --name <container-name> <image-name>
```

之后你便可以使用`docker start <container-name>`来随时启动这个容器了。这就相当于你在本地运行代码。让我们回顾一下这张图：

![image](https://github.com/user-attachments/assets/7acffeab-04d4-4fa3-bf6a-80f5a98231a6)

只要你的依赖项不变（即Dockerfile和requirements.txt不变），你便随时可以使用`docker start <container-name>`来运行你的代码。
而若是你的依赖项改变了，那你便不得不重新运行`docker build`指令了。














