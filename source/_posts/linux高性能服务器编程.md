---
title: linux高性能服务器编程
abbrlink: 96eac476
date: 2023-02-25 21:00:06
tags: [linux,server,thread,process]
categories: 阅读记录
cover: https://images.pexels.com/photos/163016/crash-test-collision-60-km-h-distraction-163016.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1
---
1. 在/etc/services中可以看到协议对应的端口号
2. arp命令可以查看修改ARP高速缓存
3. /etc/resolv.conf用来存放DNS服务器的IP地址
4. host命令用于查询主机ip
5. ip协议特点：无状态、无连接、不可靠
6. 应用层是工作在操作系统中的用户态，传输层及以下则工作在内核态。
7. 对于浏览器（客户端）中的每个标签栏都是一个独立的进程，操作系统会为这些进程分配临时的端口号。
8. 由于传输层的报文中会携带端口号，因此接收方可以识别出该报文是发送给哪个应用。
9. 网络接口层主要为网络层提供「链路级别」传输的服务，负责在以太网、WiFi 这样的底层网络上发送原始数据包，工作在网卡这个层次，使用 MAC 地址来标识网络上的设备。
10. connect之后三次握手
11. MSS 是指在TCP报文段里应用层数据的最大长度，而不是指TCP 报文段的最大长度。
12. 解析url->生成http请求 域名解析 ->协议栈处理 TCP三次握手建立连接 tcp分割数据 网络层封装 ARP解析 链路层封装 网卡 网卡驱动程序 交换机一股脑错误校验接收 交换机查询发出 路由器校验接收 路由器查询发出
13. 网卡驱动获取网络包之后，会将其复制到网卡内的缓存区中，接着会在其开头加上报头和起始帧分界符，在末尾加上用于检测错误的帧校验序列。
14. 计算机的网卡本身具有 MAC 地址，并通过核对收到的包的接收方 MAC 地址判断是不是发给自己的，如果不是发给自己的则丢弃；相对地，交换机的端口不核对接收方 MAC 地址，而是直接接收所有的包并存放到缓冲区中。因此，和网卡不同，交换机的端口不具有 MAC 地址。
15. 因为路由器是基于 IP 设计的，俗称三层网络设备，路由器的各个端口都具有 MAC 地址和 IP 地址；而交换机是基于以太网设计的，俗称二层网络设备，交换机的端口不具有 MAC 地址。
16. 在发送数据包时，如果目标主机不是本地局域网，填入的MAC地址是路由器，也就是把数据包转发给路由器，路由器一直转发下一个路由器，直到转发到目标主机的路由器，发现 IP 地址是自己局域网内的主机，就会 arp 请求获取目标主机的 MAC 地址，从而转发到这个服务器主机。
17. 
### <center>1. 线程创建<center>
```c++
#include <stdio.h>
#include <pthread.h>  // 线程库
#include <unistd.h>
#include <string.h>

//子线程处理的逻辑代码
void * callback(void * arg) {
	printf("child thread...\n");
	printf("arg value: %d\n", *(int *)arg);
	return NULL;
}

int main(){
	
	// 线程id
	pthread_t tid;
	
	int num = 10;
	// 创建子线程 tid是线程id  第二个参数设置线程属性，一般为NULL 
	// callback是子线程处理的逻辑	num是传给callback作为arg参数
	int ret = pthread_create(&tid, NULL, callback, (void *)&num);
	// 创建未成功打印错误信息
	if (ret != 0) {
		char * errstr = strerror(ret);
		printf("error: %s\n", errstr);
	}
	
	// sleep(1);
	
	for (int i = 0; i < 5; i++) {
		printf("%d\n", i);
	}

    return 0;
}
```
上面创建了一个子线程，main函数中运行代码称作**主线程**。当主线程运行完return 0后，子线程无论是否运行完都会退出，即**不保证子线程运行结束！**

继续阅读
