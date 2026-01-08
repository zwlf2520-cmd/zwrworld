# ai智能体

## 工具

> 有智能体的编辑工具有：idea ，vs，qoder ，trae（阿里）
>
> ```
> qoder： https://qoder.com/
>
> trae：  https://www.trae.cn/
> ```

## 使用

Java 的开发上面的工具都可以使用，使用前最好把jdk和maven环境配置好，不让智能体会为了配置基本环境而耗费很多时间。


# 使用 Docker 本地部署 n8n

> ```
> https://www.docker.com/
>
> 安装n8n：docker pull docker.n8n.io/n8nio/n8n
>
> 启动n8n：
>   docker run -it --rm \
>   --name n8n \
>   -p 5678:5678 \
>   -v n8n_data:/home/node/.n8n \
>   docker.n8n.io/n8nio/n8n
>
> docker stop n8n
> docker start n8n
> ```
