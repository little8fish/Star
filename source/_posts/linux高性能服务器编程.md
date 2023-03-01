---
title: linux高性能服务器编程
abbrlink: 96eac476
date: 2023-02-25 21:00:06
tags: [linux,server,thread,process]
categories: 阅读记录
cover: https://images.pexels.com/photos/163016/crash-test-collision-60-km-h-distraction-163016.jpeg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1
---

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
