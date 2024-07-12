# frpc的开机自启相关

本章节仅包含frpc的自启动指南, 不含桌面版启动器和任何第三方启动器

## Linux下自启动

(推荐使用Debian系统, 始终不推荐使用CentOs系统, !不支援 Termux 及其相關應用程式!)

下载frpc: 使用wget或其他下载工具 [下载](https://o.of.gs/client/)适合您操作系统架构的最新版本的frpc (例如: [frpc_linux_amd64.zip](https://o.of.gs/client/OpenFRP_0.57.0_e511492b_20240423/frpc_linux_amd64.tar.gz)) `下载完成后解压得到二进制可执行文件, 并赋予其执行权限 (chmod +x ./frpc_linux_amd64)`

复制下面的systemd unit服务文件内容到你的文本编辑器

```
[Unit]
Description=OpenFrp Frpc Daemon
After=network.target

[Service]
User=root
Restart=on-failure
RestartSec=5s
WorkingDirectory=/path/to/your/dir
ExecStart=/path/to/your/dir/frpc_linux_arch_file -u your_token -p your_proxy_id,your_proxy_id2,your_proxy_id3
LimitNOFILE=1048576

[Install]
WantedBy=multi-user.target
```

将工作目录和二进制可执行文件的 `绝对路径` 替换为你的实际条件

将用户token和隧道id替换为你的实际条件

将此文件命名为 `openfrp.service` 存放于目录 `/etc/systemd/system/`

执行命令启动并设置自启: 

```bash
systemctl daemon-reload
systemctl enable --now openfrp
```

查看运行状态: 

```bash
systemctl status openfrp
```

显示为 running 即代表正常运行, 如果一切顺利, 您可以在网页面板查看到隧道已启动

要更改用户token或隧道id, 请先编辑文件 `openfrp.service`, 保存后执行命令:

```bash
systemctl daemon-reload
systemctl restart openfrp
```

要删除自启动服务, 只需执行: 

```bash
systemctl disable --now openfrp
```

## docker下自启动

docker会为所有容器自启动, 这是默认值, 如果您的docker容器没有自动启动, 请自行查阅docker相关文档, 本章节不再赘述

## windows下自启动(方法1)

! 如果您使用的是桌面版启动器, 您只需要保持隧道开启即可, 本章节不适用任何桌面版启动器 !

本章节使用 `WinSW` 演示进行 `windows服务` 自启动, 

下载frpc: [点我下载](https://o.of.gs/client/)适合您操作系统架构的最新版本的frpc (例如: [frpc_windows_amd64.zip](https://o.of.gs/client/OpenFRP_0.57.0_e511492b_20240423/frpc_windows_amd64.zip)) `下载完成后解压得到exe可执行文件`

下载WinSW: [点我下载](https://github.com/winsw/winsw/releases)适合您操作系统架构的最新 Release (例如: [WinSW-x64.exe](https://github.com/winsw/winsw/releases/download/v2.12.0/WinSW-x64.exe)) `下载完成后, 将其重命名为 openfrp.exe`

复制下面的xml配置到你的文本编辑器

```xml
<service>
<name>Openfrp Frpc Daemon</name>
<description>Openfrp Frpc Daemon</description>
<id>openfrp_frpc_service</id>
<executable>frpc_windows_amd64.exe</executable>
<arguments>-u your_token -p your_proxy_id,your_proxy_id2</arguments>
</service>
```

将`frpc_windows_amd64.exe`替换为实际文件名, 将用户token和隧道id替换为你的实际条件, 并将此文件保存命名为 `openfrp.xml`

示例如图:

![img1](./image/boot-on-startup/img1.png)

现在你的文件夹下应只有三个文件, `注意: 此文件夹请放在合适的位置, 后续不能删除其中任何文件`

按住shift右键空白区域打开windows终端

![img2](./image/boot-on-startup/img2.png)

输入命令安装并启动服务:

```powershell
.\openfrp.exe install
.\openfrp.exe start
```

![img3](./image/boot-on-startup/img3.png)

如果一切顺利, 您可以在网页面板查看到隧道已启动

![img4](./image/boot-on-startup/img4.png)

如果您的隧道未能正常启动, 建议查阅同目录下 `.log` 文件获取日志来排查问题; 若仍有其他疑问, 建议入群交流 (本文档首页有群号)

要删除服务, 请执行如下命令, 然后删除整个文件夹即可

```powershell
.\openfrp.exe stop
.\openfrp.exe uninstall
```

要更改用户token或隧道id, 请先停止并卸载服务, 然后编辑xml文件内容, 保存后再执行上述安装&启动命令即可

## windows下自启动(方法2)

1.获取脚本

打开网页版openfrp 点击管理隧道

选择你要开机自启动的节点,点击按钮   

选择”获取配置文件/启动命令”复制启动命令
 
2.制作脚本

在你放frpc的位置新建一个文本文档

打开文档把刚才复制的东西粘贴进去

./frpc -u xxxx -p xxxx

复制你frpc的名称,比如我的frpc文件就叫frpc_windows_386.exe

那么我就改成

frpc_windows_386.exe -u xxxx -p xxxx

保存并退出

重命名为start.cmd
                    
运行试试能不能启动frpc如果可以就下一步

3.设置启动

打开菜单栏,右键启动文件夹,选择打开
                            
右键刚才制作的脚本 选择新建快捷方式
                 
将快捷方式复制到启动文件夹内
                 
接下来,你就可以享受开机自启动的服务了

如果你不想要了,就去把快捷方式删了

试用期间,千万不要删除自己做的脚本和快捷方式和frpc因为这只是个快捷方式

由于此办法是用系统自带来实现的

所以并未做到隐藏cmd窗口

## macos下自启动

To be continued
