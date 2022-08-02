<h1> <img src="https://i.imgur.com/UIQSMEs.png" height=45> Risk of Rain 2 dockerized server </h1>

[![Docker Pulls](https://img.shields.io/docker/pulls/zzcabc/ror2server?style=flat-square)](https://hub.docker.com/r/zzcabc/ror2server)


本项目Fork 自 [avivace/ror2server](https://github.com/avivace/ror2-server)

因为原项目下载Risk of Rain 2 Server过于缓慢，本项目构建的时候自动下载并添加至容器内部，依旧每次启动更新

由于Risk of Rain 2 Server 原因，本项目只能在AMD64架构的CPU上面运行


运行命令
```bash
docker run -d \
    --name ror2server \
    -p 27015:27015/udp \
    zzcabc/ror2server:latest
```

在《Risk of Rain 2》中 使用<kbd>CTRL</kbd> + <kbd>ALT</kbd> + <kbd>\`</kbd> 启动控制台，并输入`connect "<SERVER_IP>:27015";` 即可连接服务器


如果你想使用其他端口比如**25000**和密码**helloworld**

可以使用下面命令
```sh
docker run -d \
    -p 25000:25000/udp \
    --name ror2server \
    -e R2_SV_PORT=25000 \
    -e R2_PSW='helloworld' \
    zzcabc/ror2server:latest
```
在控制台中输入下面代码即可加入`cl_password "helloworld"; connect "<SERVER_IP>:25000";`

你可以使用环境变量对 Risk of Rain 2 Server 进行配置：

- `R2_PLAYERS`: 最大支持玩家数量， 默认 `4`， 最高 `16`;
- `R2_HEARTBEAT`: 设置为 `1`， 向《Risk of Rain 2》的大厅中添加你的服务器。 如果你设置此配置， 记得在Docker命令中添加 `-p 27016:27016/udp` ;
- `R2_HOSTNAME`: 设置大厅中服务器的名称，默认 `Risk of Rain 2 dockerized server` ;
- `R2_PSW`: 加入服务器的密码，默认无密码;
- `R2_ENABLE_MODS`: 设置为 `1` 启动对Mod的支持 (请添加Mod的相关文件及文件夹 [Mod支持](#Mod支持));
- `R2_QUERY_PORT`: 用于 Steamworks 连接的监听端口，记得在Docker命令中添加 `-p <PORT>:<PORT>/udp`，默认`27015`;
- `R2_SV_PORT`: 服务器的监听端口，大厅中显示的端口，记得在Docker命令中添加 `-p <PORT>:<PORT>/udp`，默认`27016`;
- `R2_TAGS`: 服务器将在服务器浏览器中具有的标签.
- `R2_GAMEMODE`: 运行的游戏模式类型，默认为`ClassicRun`. 支持下面选项:
    - `ClassicRun` (Standard)
    - `InfiniteTowerRun` (Simulacrum)

如果不打算在同一个IP或者主机上面运行多个服务器，尽量不要改动`R2_QUERY_PORT` 和 `R2_SV_PORT`。

一键运行命令

```sh
docker run -d \
    --name ror2server \
    -p 27015:27015 \
    -p 27016:27016 \
    -e R2_HEARTBEAT=1 \
    -e R2_HOSTNAME='Risk of Rain 2 dockerized server' \
    -e R2_PSW='password' \
    zzcabc/ror2server:latest
```

要检查服务器是否正确地发布到 Steamworks 网络，可以使用此 API 调用:

```bash
curl http://api.steampowered.com/ISteamApps/GetServersAtAddress/v0001/?format=json&addr=<IP_ADDRESS>
```

## Mod支持

要安装和启用 mods 服务器端，需要一个目录，其中包含:

- 带有所需模组的 **BepInEx**;
- `doorstop_config.ini` 和 `winhttp.dll` 文件配置**BepInEx** 一起使用

如果你的mod文件夹是 `/path/to/directory`, 使用下面的命令启动:

```bash
docker run -d \
    -p 27015:27015/udp \
    --name ror2server \
    -v /path/to/directory:/root/ror2ds-mods \
    -e R2_ENABLE_MODS=1 \
    zzcabc/ror2server:latest
```

注意：有一些Mod需要在客户端安装

## Known Issues

Be aware that this version suffers from some [known issues](https://github.com/avivace/ror2-server/issues?q=is%3Aissue+is%3Aopen+label%3Abug), probably caused by the executable not running natively on Windows. You should probably [ask the developers](https://twitter.com/riskofrain) for a proper Linux build.

Since the RoR2 Server will be downloaded each time the docker container is started, there could be breaking changes which require more up to date versions of wine in order to work correctly. In this case you can try forcing the installation of bleeding-edge wine versions by running:
```bash
# wine-devel
docker run ${your_parameters} -e WINE_REPLACE_REL="devel" zzcabc/ror2server:latest
# wine-staging
docker run ${your_parameters} -e WINE_REPLACE_REL="staging" zzcabc/ror2server:latest
```

## FAQ

##### Can I run this on a VPS?

Yes, any Linux box works. For decent performance, you need 3 GB of free space and at least 2 GB of RAM.


##### Server is stuck at "Unloading unused Assets"

That line is usually the last one of the initialization process. It usually means your server is working correctly, that is not a blocking error. If you can't connect to your server at that point, it's probably a network issue.


##### Server is stuck at "Could not load config ..."

If you see something like this:

```
Could not load config /Config/server_pregame.cfg: Could not find file "Z:\home\steam\ror2-dedicated\Risk of Rain 2_Data\Config\server_pregame.cfg"
```

Be aware that these kind of warning messages are non blocking, they are just warnings and the server initialization will proceed as normal.


### Acknowledgements

Thanks to [InfernalPlacebo](https://github.com/InfernalPlacebo) and [Vam-Jam](https://github.com/Vam-Jam).

Built by [Manuele](https://github.com/dubvulture), [Davide Casella](https://github.com/dcasella), [Fabio Nicolini](https://github.com/fnicolini), [Antonio Vivace](https://github.com/avivace).
