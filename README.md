# Shadowsocks-with-frp
树莓派搭建Shadowsocks服务并使用frp

1、介绍
Shadowsocks是一款主流科学上网软件，为我们提高生产力提供了非常大的帮助。随着xxx对科学上网行为的管控越来越严格，很多山寨机场纷纷倒下，像搬瓦工、Linode，Vultr，Digital Ocean这些名胜在外的机场也岌岌可危。我们不难想象在2019的某个早晨，我们的小飞机再也降落不了了。因此，我们有必要修一个自己的机场。
修机场不难，有很多一键安装包可以使用。但是很多机场没有自己的公网IP，所以还需要找个内网穿透的服务。说起内网穿透啊，就想到花生壳，但是花生壳的服务要换域名而且也不稳定，已经两开花啦。所以还是试试别的。比如frp或者ngrok 。所以这个项目解决的需求就是给自己造个机场，因为只自己用，一个树莓派就够了，放在哪都不扎眼也不费电。同理，用路由器也可以制作。此外，这个小制作同时也适用于常年出差但单位没开VPN或者级别不够的科研狗、工程师和学生朋友，下文件，查资料都非常方便，实在是居家旅行，出门必备好物。
类似的工作在网上看到过几位前辈写的指南，为我建设机场起到了巨大的帮助，不过他们帖子中关于frp配置方面和VPS的调试方面没有说太清楚，我觉得如果是和我一样这种需求导向型的人来做的话可能会有些困惑，因此做个笔记让大家少走我已经走过的弯路。
特别提示：如果用此法科学上网要将树莓派部署到不需要科学上网的地区。

2、硬件和软件环境

硬件：
树莓派B一个（2011.12的老古董）
网线一根 USB电源线一根
某云VPS（有公网IP）
没有墙的LAN口一个

软件：
树莓派装原版系统（Raspbian Stretch with desktop and recommended software）
一代树莓派选择范围有限
frp（https://github.com/fatedier/frp/）
这个工具很好，据说能直接翻，但是我不太会。
shadowsocks-libdev（https://github.com/shadowsocks/shadowsocks-libev）
尽量用稳定的吧，也能保证安全生产

3、过程
1）树莓派刷好系统（略）
2）做好网络设置
树莓派一代没有无线网卡，而且我认为有线网简易而稳定。
对了，如果贵司也使用了802.1X认证，欢迎参考我过两天再写的一个笔记。
3）树莓派装shadowsocks-libdev
首先使用root登录，之后一键安装
wget --no-check-certificate -O shadowsocks-libev-debian.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-libev-debian.sh
chmod +x shadowsocks-libev-debian.sh
./shadowsocks-libev-debian.sh 2>&1 | tee shadowsocks-libev-debian.log
这部分网上太多教程了，有问题可看前面github的介绍，需要注意的是，在配置时要设置【ss中配置的端口】（贵司防火墙没有封的）。
装好之后，就这样
Your Server IP        :  123.123.123.122 
Your Server Port      :  8888 
Your Password         :  password 
Your Encryption Method:  aes-256-cfb 
4）frp配置
分VPS和树莓派两部分，我参考的这个
https://lolico.moe/tutorial/frp.html
首先配置VPS，环境CentOS 7.4 64位
从git下载frp，注意架构别下错了
https://github.com/fatedier/frp/releases
安装过程上面都有，我主要说一下 frps.ini的ss部分的配置
“
[shadowsocks]
type = tcp
bind_addr = 0.0.0.0
listen_port = 【服务器端监听端口】
”
全部配置可参考项目中的文件

树莓派部分也是，先从git下载最新的版本，还是搞清楚架构
frpc.ini 部分配置
“
[shadowsocks]
type = tcp
local_ip =127.0.0.1
local_port = 【ss中配置的端口】
remote_port = 【服务器端监听端口】
”
做好之后，通通重启。

5）VPS端的安全策略
这部分教程中没提到，我在这卡了半天。很多云服务器“贴心地”帮你关掉了除了22 443 80等常用端口外所有的入出站端口。
请根据不同的VPS提供商的设置进行调整。保证以上所提及到的端口放通。






