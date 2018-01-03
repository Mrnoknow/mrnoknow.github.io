title: 在vm下 Ubuntu server网络配置
date: 2016-04-30 15:31:00
categories: [虚拟机,Ubuntu server]
tags: other
---

在VM（win7）下Ubuntu server的网络配置，仅提供我解决的方式。（也是查阅许多资料得到的）


![本机分配IP](/images/在vm下 Ubuntu server网络配置/1.jpg)

我的IPV4 地址首选地址为   _192.168.254.1_  ,所以我将虚拟机中NET连接的网关设置为 _192.168.254.1_。

如图：
            ![设置vm的NET网关](/images/在vm下 Ubuntu server网络配置/2.png)



当然不要忘记把子网IP(network)改成 _192.168.254.0_;保证在同一网段。

如图:
![设置vm的NET子网IP](/images/在vm下 Ubuntu server网络配置/3.jpg)

之后设置Ubuntu server 中的IP，网关，network，DNS。

* 设置IP、网关等。sudo vim /etc/network/interfaces，设置如下
![设置Ubuntu IP 网关 network 网关 DNS](/images/在vm下 Ubuntu server网络配置/5.png)
在此这个IP 也是有范围的,查看 _DHCP设置_ 
![IP范围](/images/在vm下 Ubuntu server网络配置/4.png)

我们看到我的可用IP地址为 _192.168.199.254.128~192.168.199.254.254_,所以我们设置的IP地址必须为此范围内。

* 设置hosts文件中的IP,sudo vim /etc/hosts
![IP设置](/images/在vm下 Ubuntu server网络配置/6.png)
此地址与1步骤中的IP一致。

* 设置DNS服务器,sudo vim /etc/resolv.conf 
![DNS服务器设置](/images/在vm下 Ubuntu server网络配置/7.png)

完成以上，重启服务就OK了。

** 在这里着重提醒一下，网关和dns服务器尽量填写首选ip地址（192.168.254.1）不然在后面会出现host unreachable这个问题 **