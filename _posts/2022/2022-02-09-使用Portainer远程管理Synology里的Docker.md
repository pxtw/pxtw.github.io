---
layout: post
title: '使用Portainer远程管理Synology里的Docker'
date: 2022-02-09T15:08:02+11:00
location: '赣州'
---

因为我的Portainer在另外一台设备（OMV）上，而我又不想在群晖的Docker里再额外安装一个Portainer。

所以我决定使用群晖的`dockerd`监听一个远程的TCP连接，然后用OMV上的Portainer去连。

1. `ps aux | grep docker`

发现 
```
root     13394  0.0  0.2 1558524 35552 ?       Ssl  15:01   0:00 /var/packages/Docker/target/usr/bin/dockerd --config-file /var/packages/Docker/etc/dockerd.json
root     14500  0.0  0.2 1557112 40564 ?       Ssl  15:01   0:00 containerd --config /var/run/docker/containerd/containerd.toml --log-level info
```

2. `vim /var/packages/Docker/etc/dockerd.json`

3. 添加 `"hosts":["tcp://0.0.0.0:2375", "unix:///var/run/docker.sock"]`

4. `kill 13394` , 之后 dockerd会被再次启动（我也并不知道为什么）。 

5. 在OMV Portainer里`Environment -> Add environment -> Docker`, 设置 `Environment URL: ${LAN_IP}:2375`
