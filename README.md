# MyCustomDrissionPage

fork版本 4.1.0.17

原仓库地址 https://github.com/g1879/DrissionPage

主要改动为对容器的支持。

## 开发环境

支持在开发容器中连接宿主机浏览器，因为DrissionPage和浏览器之间其实只依靠websocket连接。

目前可以在容器中运行DrissionPage，通过host.docker.internal访问宿主机开启了debug端口的浏览器。

docker-compose强制开启host.docker.internal:
```yaml
services:
  drissionpage-dev:
    ...
    extra_hosts:
      - host.docker.internal:host-gateway
```

启动浏览器并开启debug端口
```powershell
# edge
msedge --remote-debugging-port=9999
# chrome
chrome --remote-debugging-port=9999
```

然后容器内通过DrissionPage连接浏览器：
```python
from DrissionPage import ChromiumPage,ChromiumOptions

page = ChromiumPage(
    ChromiumOptions()
    .set_address("host.docker.internal:9999")
    .existing_only()
)
page.get("https://www.baidu.com")
```

### 浏览器的debug端口打不开？
1. 启动之前检查是否关闭了所有的chrome/edge进程，不要留后台。
2. 地址栏访问`chrome://version`或者`edge://version`，检查启动命令中有无remote-debugging-port参数
3. windows系统使用`netstat -aon|findstr "8081"`命令检查端口占用情况


## todo:
1. 自动启动宿主机浏览器(？可能通过容器ssh控制windows实现)
2. 在容器内使用无头模式运行
3. 在容器内有桌面环境的情况下以有头模式运行
   1. 已有尝试，参考 https://hub.docker.com/r/qiudeng/drissionpage4-ubuntu-webdesktop-novnc