# MrRSS Docker

基于 [WCY-dt/MrRSS](https://github.com/WCY-dt/MrRSS) 的 Docker 镜像，提供完整的服务器版本部署方案。

## 特性

- 🚀 预编译前端和服务器二进制文件
- 🔒 以非 root 用户运行，提高安全性
- 💾 支持数据持久化
- 🏥 内置健康检查
- 📦 多架构支持（linux/amd64, linux/arm64）

## 快速开始

### 使用 Docker

```bash
docker run -d \
  --name mrrss \
  -p 1234:1234 \
  -v mrrss-data:/app/data \
  ghcr.io/batkiz/mrrss:latest
```

### 使用 Docker Compose

```yaml
version: '3.8'

services:
  mrrss:
    image: ghcr.io/batkiz/mrrss:latest
    container_name: mrrss
    restart: unless-stopped
    ports:
      - "1234:1234"
    volumes:
      - ./data:/app/data
    environment:
      - TZ=Asia/Shanghai
    healthcheck:
      test: ["CMD", "wget", "--no-verbose", "--tries=1", "--spider", "http://localhost:1234/api/version"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 5s
```

## 配置说明

### 端口

- **1234**: MrRSS 服务端口

### 数据卷

- `/app/data`: 数据持久化目录，存储订阅源和配置信息

### 环境变量

- `TZ`: 时区设置（可选，默认 UTC）

### 镜像标签

- `latest`: 最新稳定版本
- `v1.x.x`: 特定版本标签（对应上游版本号）

## 数据持久化

建议将数据目录挂载到宿主机以确保数据不会丢失：

```yaml
volumes:
  - ./data:/app/data
```

或使用 Docker volume：

```bash
docker volume create mrrss-data
docker run -d --name mrrss -p 1234:1234 -v mrrss-data:/app/data ghcr.io/batkiz/mrrss:latest
```

## 构建镜像

```bash
docker build -t mrrss:latest .
```

## 健康检查

容器包含健康检查，每 30 秒检查一次服务状态，可通过以下命令查看：

```bash
docker inspect --format='{{.State.Health.Status}}' mrrss
```

## License

本项目遵循上游 [WCY-dt/MrRSS](https://github.com/WCY-dt/MrRSS) 的许可证。
