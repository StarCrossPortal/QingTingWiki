# 蜻蜓工作台节点接入方法
<hr>


&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;蜻蜓安全工作台是一个安全工具集成平台，集成市面上主流的安全工具，并按照工作场景进行编排，目前主要预制了四个场景：信息收集、黑盒扫描、POC批量验证、代码审计；

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;蜻蜓工作台的最大特点就是集成的工具多、种类全，你可以将你想要的工具编排成任意一个场景，快速打造属于自己的安全工作台~
- 视频教程：https://www.bilibili.com/video/BV1uR4y1P7vp/
- 官网地址：http://qingting.starcross.cn/
<hr>

><li>星阑科技产品 <code>蜻蜓安全工作台</code> 仅授权你在遵守《<a href="https://baike.baidu.com/item/%E4%B8%AD%E5%8D%8E%E4%BA%BA%E6%B0%91%E5%85%B1%E5%92%8C%E5%9B%BD%E7%BD%91%E7%BB%9C%E5%AE%89%E5%85%A8%E6%B3%95" target="_blank">中华人民共和国网络安全法</a>》前提下使用，请不要对未获得授权的目标进行安全测试！！！
><hr>


##  一、Docker 远程访问TLS加密访问|蜻蜓

###  1.1 下载认证文件
![QQ截图20220430170856](http://halo.itmuzi.cc/upload/2022/04/QQ%E6%88%AA%E5%9B%BE20220430170856.png)

```![5](http://halo.itmuzi.cc/upload/2022/04/5.png)![5-1651313145464](http://halo.itmuzi.cc/upload/2022/04/5-1651313145464.png)
  curl -L http://qingting.starcross.cn/static/server.zip > /server.zip   && cd / && unzip server.zip
```

![2](http://halo.itmuzi.cc/upload/2022/04/2.png)
#### 报错：
如果安装出现-bash： unzip未找到命令

![3](http://halo.itmuzi.cc/upload/2022/04/3.png)
使用yum安装unzip，方可解决

![4](http://halo.itmuzi.cc/upload/2022/04/4.png)

### 1.2 添加启动参数

```
  mkdir -p /etc/systemd/system/docker.service.d/
```

![5-1651313156586](http://halo.itmuzi.cc/upload/2022/04/5-1651313156586.png)

将下列整段复制进命令行
```
echo "[Service]
ExecStart=
ExecStart=/usr/bin/dockerd -H unix:///var/run/docker.sock -H tcp://0.0.0.0:2376 --tlsverify --tlscacert=/server/ca.pem --tlscert=/server/server-cert.pem --tlskey=/server/server-key.pem" > /etc/systemd/system/docker.service.d/override.conf
```
![6](http://halo.itmuzi.cc/upload/2022/04/6.png)

### 1.3 置并重启服务
```
systemctl daemon-reload  && systemctl restart docker
```
![7](http://halo.itmuzi.cc/upload/2022/04/7.png)

如果没有出现任何提示说明上面配置正确可以进入下面的配置
#### 检查:
可以安装net-tools检查2376是否成功运行

![8](http://halo.itmuzi.cc/upload/2022/04/8.png)
```
  netstat -ntl
```

![9](http://halo.itmuzi.cc/upload/2022/04/9.png)

## 二、FRPC 打通内网机器到公网
### 2.1 下载FRPC
```
curl -L http://qingting.starcross.cn/static/frpc > ~/frpc  && chmod 755 ~/frpc
```
![10](http://halo.itmuzi.cc/upload/2022/04/10.png)

### 2.2 添加配置
```
vi ~/frpc.ini
```
![11](http://halo.itmuzi.cc/upload/2022/04/11.png)
```
[common]
server_addr = 49.233.184.147
server_port = 7000

[rap-替换成你的用户名] //整个名字都可以改
type = tcp
local_ip = 127.0.0.1
local_port = 2376
remote_port = 50005(替换成任意50000~60000)
```
![15](http://halo.itmuzi.cc/upload/2022/04/15.png)

退到`~`目录下,查看到两个文件
![13](http://halo.itmuzi.cc/upload/2022/04/13.png)

![18](http://halo.itmuzi.cc/upload/2022/04/18.png)

2.3 启动FRPC
```
cd ~/ && nohup ./frpc &
```

![14](http://halo.itmuzi.cc/upload/2022/04/14.png)

可以通过tail -f nohup.out查看文件实时报错
![19](http://halo.itmuzi.cc/upload/2022/04/19.png)

### 2.4 常见错误
#### 2.4.1 启动错误：端口已被使用
![16](http://halo.itmuzi.cc/upload/2022/04/16.png)
将你的端口修改后再次启动

#### 2.4.2 启动错误：代理名称`[rap ictmuzi]`已在使用中
![17](http://halo.itmuzi.cc/upload/2022/04/17.png)
将你的代理名称修改后再次启动


##  三、添加工作节点
### 3.1主机名随便填写docker的IP地址填写49.223.184.147端口填写刚刚配置的`remote_port`点击提交即可
![21](http://halo.itmuzi.cc/upload/2022/04/21.png)

![22](http://halo.itmuzi.cc/upload/2022/04/22.png)
### 3.2添加项目
![23](http://halo.itmuzi.cc/upload/2022/04/23.png)
![24](http://halo.itmuzi.cc/upload/2022/04/24.png)
### 3.3 添加目标
![25](http://halo.itmuzi.cc/upload/2022/04/25.png)
![26](http://halo.itmuzi.cc/upload/2022/04/26.png)
### 3.4扫描结果
![微信图片_20220430184527](http://halo.itmuzi.cc/upload/2022/04/%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20220430184527.jpg)

 