## a simple server for sending and receiving messages

- sending messages via a REST-API
- subscribing/receiving messages via a web socket connection
- managing users, clients and applications

## Docker Compose
```
services:
  gotify:
    image: gotify/server
    container_name: gotify
    ports:
      - "8005:80"
    environment:
      - TZ="Aisa/Hong Kong"
      - GOTIFY_DEFAULTUSER_PASS=SuperSafePassword123
    volumes:
      - data:/app/data
    restart: unless-stopped
    networks:
      - gotify

volumes:
  data:
    name: gotify_data

networks:
  gotify:
    name: gotify
```

使用上也非常简单，总的来说，在 Gotify 的 Web UI 除了像 Android 端一样可以收到消息通知外，还附带了一些简单的管理功能，除去用户密码的管理，剩下需要管理的大体分为两类。

- 客户端（Clients）
- 应用（Apps）

### 客户端（Clients）[](https://blog.yuanji.dev/posts/getting-started-with-gotify/#%e5%ae%a2%e6%88%b7%e7%ab%afclients)

客户端的话最好理解，比如说我们登录上 Web UI，这个 Web 算一个客户端，如果在 Android 上登录的话，那也是一个客户端。总之就是用来接收通知的设备就对了，一般不需要单独做什么操作，除非你想删除某个设备。

![Gotify Web UI 上管理客户端的样子](https://blog.yuanji.dev/posts/getting-started-with-gotify/gotif-web-ui-clients-screenshot_hu12730567977401019069.webp)

Gotify Web UI 上管理客户端的样子

### 应用（Apps）[](https://blog.yuanji.dev/posts/getting-started-with-gotify/#%e5%ba%94%e7%94%a8apps)

应用的话，则像是一个分类的功能，拿聊天软件类比的话，有点儿像频道。创建一个应用的话就会生成一个单独的 Token，可以拿那个 Token 作为凭证来发送消息。

推送消息的话，就是一个简单的 POST 方法的 HTTP 请求，详细可以参考官方文档 [Push messages · Gotify](https://gotify.net/docs/pushmsg) 或是位于 `/docs` 的 API 文档。

```bash
curl "https://push.example.de/message?token=<apptoken>" -F "title=my title" -F "message=my message" -F "priority=5"
http -f POST "https://push.example.de/message?token=<apptoken>" title="my title" message="my message" priority="5"
```

其中的 `priority` 代表了优先级，数字越大代表了优先级越高，主要和在客户端接收到消息后展示的顺序有关。

消息发送成功后就会自动推送到各个客户端了。另外为了让消息的分类更为一目了然，它自带了一个上传应用封面的功能，就像我下面截图展示的那样，那些应用的封面的图片是我自己上传的。

![Gotify Web UI 上管理应用的样子](https://blog.yuanji.dev/posts/getting-started-with-gotify/gotif-web-ui-apps-screenshot_hu12682706152941339306.webp)

Gotify Web UI 上管理应用的样子

## Example send sample message in Windows desktop

```
curl "http://192.168.50.241:8005/message?token=AzGOv-UKEJY_IRw" -F "title=my title" -F "message=my message" -F "priority=5"

http -f POST "http://192.168.50.241:8005/message?token=AzGOv-UKEJY_IRw" title="my title" message="my message" priority="5"
```

## Gethomepage widget

```
widget:
  type: gotify
  url: http://gotify.host.or.ip
  key: clientoken
```

