Watchtower 是一個用 [Go 語言](https://go.dev)開發的應用程序，它會監視正在運行的 Docker 容器，並觀察這些容器最初啟動時所使用的映像檔 (Docker Image) 是否有更改。如果 watchtower 檢測到映像檔已更改，它將自動使用新映像檔重新啟動容器。

透過 watchtower，開發者可以通過將新的映像檔推送到 Docker Hub 或您自己的 Docker Registry，簡單地更新容器化應用程序的運行版本。Watchtower 將下載您的新映像，優雅地關閉現有容器，然後使用最初部署時使用的相同選項重新啟動它。

例如，假設您正在運行 watchtower 以及一個名為 `ghcr.io/go-training/example53` 的映像實例：

每隔幾分鐘，watchtower 將下載最新的 `ghcr.io/go-training/example53` 映像檔並將其與用於運行 “example53” 容器的映像進行比較。如果它發現映像檔已更改，它將停止/刪除 “example53” 容器，然後使用新映像和最初啟動容器時使用的相同 docker run 選項重新啟動它。

## 使用方式

Watchtower 本身被打包成 Docker 容器，因此安裝非常簡單，只需拉取 containrrr/watchtower 映像即可。如果您正在使用基於 ARM 的架構，請從 Docker Hub 拉取適當的 `containrrr/watchtower:armhf-tag` 映像。

由於 watchtower 代碼需要與 Docker API 進行交互以監視運行的容器，因此在運行容器時需要使用 -v 標誌將 /var/run/docker.sock 挂載到容器中。

使用以下命令運行 watchtower 容器

```sh
docker run -d \
  --name watchtower \
  -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower
```
如果從私有 Docker 註冊表中拉取映像檔，請使用環境變數 `REPO_USER` 和 `REPO_PASS` 或將主機的 docker 配置文件掛載到容器中（位於容器文件系統的根目錄 /）。
```sh
docker run -d \
  --name watchtower \
  -e REPO_USER=username \
  -e REPO_PASS=password \
  -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower container_to_watch --debug
```

另外，如果您在 Docker Hub 上設置了 2FA 身份驗證，提供帳號和密碼將不足夠。相反，可以運行 docker login 命令將憑證存在 `$HOME/.docker/config.json` 文件中，然後掛載此配置文件以使其對 Watchtower 容器可用：

```sh
docker run -d \
  --name watchtower \
  -v $HOME/.docker/config.json:/config.json \
  -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower container_to_watch --debug
```

## Docker Compose

```yaml
version: "3"
services:
  watchtower:
    image: containrrr/watchtower
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      - WATCHTOWER_NOTIFICATIONS=gotify 
      - WATCHTOWER_NOTIFICATION_GOTIFY_URL=http://192.168.50.241:8005/ 
      - WATCHTOWER_NOTIFICATION_GOTIFY_TOKEN=AbIJRh9Ibkg1LFN 
    hostname: watchtower
    command: --cleanup --interval 86400
    restart: always
```
