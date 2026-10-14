# IDL Service Design Studio · Docker Compose

用 Docker Compose 在 **NAS / 服务器** 上一条命令部署 [IDL Service Design Studio](https://github.com/raysico/IDLstudio)（内部 IDL 可视化设计台，实时编译 OpenAPI 3.1 / protobuf-gRPC，内置 mock 调试台）。

## 快速开始

前置：NAS 已安装 **Docker** 与 **Docker Compose**（群晖 / 威联通 / Unraid 等 Docker 套件均含）。

```bash
# 下载本仓库
git clone https://github.com/raysico/IDLstudioDockercompose.git
cd IDLstudioDockercompose

# 启动
docker compose up -d
```

浏览器访问：`http://<NAS局域网IP>:8010`

## 从源码构建（镜像未发布时的替代）

镜像发布前，可在任意装有 Docker 的机器上从源码构建：

```bash
git clone https://github.com/raysico/IDLstudio.git
cd IDLstudio
docker build -t ghcr.io/raysico/idlstudio:1.0.0 .
# 然后回到 compose 目录 docker compose up -d
```

## 数据持久化

项目数据（`project.json`）写入命名卷 `idlstudio-data`，重建 / 升级容器不丢失。

如需把数据放到指定目录（例如 NAS 共享盘），把 `docker-compose.yml` 的卷改为 bind mount：

```yaml
    volumes:
      - /volume1/docker/idlstudio:/app/data
```

## 升级

```bash
docker compose pull && docker compose up -d
```

## ⚠️ 安全边界（务必阅读）

- 本工具**没有登录鉴权**，任何能访问该端口的人都能查看 / 修改 IDL 项目数据。
- 默认 `0.0.0.0` 监听 + 端口映射后，**同一局域网内所有设备**都能访问。
- **请仅在可信局域网内使用**；若需通过公网访问，务必在前面加一层带鉴权的反向代理（如 Caddy / Nginx + Basic Auth），**不要**直接把 8010 端口映射到公网。

## 配置项

| 项 | 默认 | 说明 |
|---|---|---|
| 端口 | `8010:8010` | 只限本机访问可改 `127.0.0.1:8010:8010` |
| 数据卷 | `idlstudio-data` | 或 bind mount 到 NAS 目录 |
| 镜像 | `ghcr.io/raysico/idlstudio:1.0.0` | 多架构（amd64 / arm64） |
