# docker部署samba

docker-compose.yaml

```yaml
services:
  samba:
    image: dperson/samba  # 使用 dperson/samba 镜像
    container_name: samba  # 容器名称设置为 samba
    environment:
      - TZ=Asia/Shanghai  # 设置容器的时区为 Asia/Shanghai
    volumes:
      - ${PWD}/shared:/shared  # 将主机当前目录下的 shared 文件夹挂载到容器的 /shared 目录
    ports:
      - "137:137/udp"  # 映射 UDP 137 端口（NetBIOS Name Service）
      - "138:138/udp"  # 映射 UDP 138 端口（NetBIOS Datagram Service）
      - "139:139/tcp"  # 映射 TCP 139 端口（NetBIOS Session Service）
      - "445:445/tcp"  # 映射 TCP 445 端口（SMB over TCP）
    command: >  # 使用 command 参数传递 Samba 配置
      -s "video;/shared/video;yes;no;yes;all;all;all;视频"
      -u "root;root"
      -w "WORKGROUP"
    restart: unless-stopped  # 设置容器重启策略为 unless-stopped（除非手动停止，否则始终重启）
```

- 使用 `command` 参数传递 Samba 配置：
  - `-s "video;/shared/video;yes;no;yes;all;all;all;视频"`：
    - `video`：共享名称。
    - `/shared/video`：共享目录路径。
    - `yes`：共享目录可浏览。
    - `no`：共享目录不可写。
    - `yes`：允许匿名访问。
    - `all`：允许所有用户访问。
    - `all`：允许所有用户写入。
    - `all`：允许所有用户创建文件。
    - `视频`：共享描述。
  - `-u "root;root"`：添加用户 `root`，密码为 `root`。
  - `-w "WORKGROUP"`：设置工作组为 `WORKGROUP`。