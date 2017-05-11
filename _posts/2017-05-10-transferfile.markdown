---
layout:     post
title:      "基于TCP/IP的Linux文件传输程序开发记录"
subtitle:   ""
description: "TCP Linux socket"
date:       2017-05-10 01:00:00
author:     "Scalpel"
header-img: "img/home-bg-o.jpg"
tags:
- Linux
- 网络编程
---
　　前段时间读完了《APUE》和《UNP》第一卷，打算写个项目练练手，正好在实验室里和同学之间传文件不太方便，于是打算写个传输文件的小程序[项目主页](https://github.com/scalpelx/Transfer_File)   
　　首先编写发送端也就是客户端的程序，程序的大体流程为：创建套接字，设置服务器端套接字地址，调用connect创建连接并等待服务器端接受，连接建立后根据输入的选项获取文件名，发送文件名（*在这里折腾
了很长时间，因为TCP有发送缓冲区，send的数据并不会立即发送，这样后面会跟文件数据，直到缓冲区满，一起发送出去，这样接收端获取文件名不太方便，需要记录文件名长度来提取什么的，我这里是把文件名放进
跟缓冲区大小一样大的字符串里，一下发送出去，目前还在想有没有更好的方法，如果你有更好的方法，欢迎指教*），最后打开文件，读取数据并发送出去（*因为fread读的数据不能确保是你指定的数目，所以需要记
录下fread读的字节数，也就是其返回值，把它作为发送的字节数，并且读的字节数和指定的不一致时，需要判断是读到文件结尾还是中间出错*），以下为发送端代码`send_file.c`:


```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <libgen.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <netinet/in.h>
#include <sys/socket.h>
#include "transfer.h"

void sendfile(FILE *fp, int sockfd);

int main(int argc, char* argv[])
{
    //判断参数
    if (argc != 3) 
    {
        perror("usage:send_file filepath <IPaddress>");
        exit(1);
    }

    //创建TCP套接字
    int sockfd = socket(AF_INET, SOCK_STREAM, 0);
    if (sockfd < 0) 
    {
        perror("Can't allocate sockfd");
        exit(1);
    }

    //设置传输对端套接字地址
    struct sockaddr_in serveraddr;
    memset(&serveraddr, 0, sizeof(serveraddr));
    serveraddr.sin_family = AF_INET;
    serveraddr.sin_port = htons(SERVERPORT);
    if (inet_pton(AF_INET, argv[2], &serveraddr.sin_addr) < 0) //将IP地址格式从字符串转换为二进制
    {
        perror("IPaddress Convert Error");
        exit(1);
    }

    //建立连接
    if (connect(sockfd, (const struct sockaddr *) &serveraddr, sizeof(serveraddr)) < 0)
    {
        perror("Connect Error");
        exit(1);
    }
    
    //获取文件名
    char *filename = basename(argv[1]); //文件名
    if (filename == NULL)
    {
        perror("Can't get filename");
        exit(1);
    }
    
    /*发送文件名
      为了将文件名一次发送出去，而不是暂存到TCP发送缓冲区中，避免对方收到多余的数据，不好解析正确的文件名，
      需要将要发送的数据大小设置为缓冲区大小*/
    char buff[BUFFSIZE] = {0};
    strncpy(buff, filename, strlen(filename));
    if (send(sockfd, buff, BUFFSIZE, 0) == -1)
    {
        perror("Can't send filename");
        exit(1);
    }
    
    //打开要发送的文件
    FILE *fp = fopen(argv[1], "rb");
    if (fp == NULL) 
    {
        perror("Can't open file");
        exit(1);
    }

    //读取并发送文件
    sendfile(fp, sockfd);
    puts("Send Success");

    //关闭文件和套接字
    fclose(fp);
    close(sockfd);
    return 0;
}

void sendfile(FILE *fp, int sockfd) 
{
    int n; //每次读取数据数量
    char sendline[MAX_LINE] = {0}; //暂存每次读取的数据
    while ((n = fread(sendline, sizeof(char), MAX_LINE, fp)) > 0) 
    {
        if (n != MAX_LINE && ferror(fp)) //读取出错并且没有到达文件结尾
        {
            perror("Read File Error");
            exit(1);
        }
        
        //将读取的数据发送到TCP发送缓冲区
        if (send(sockfd, sendline, n, 0) == -1)
        {
            perror("Can't send file");
            exit(1);
        }
        memset(sendline, 0, MAX_LINE); //清空暂存字符串
    }
}
```

　　公共头文件`transfer.h`：


```c
#ifndef TRANSFER_FILE_TRANSFER_H
#define TRANSFER_FILE_TRANSFER_H

//读写数据大小
#define MAX_LINE 4096
//监听端口
#define LINSTENPORT 7788
//服务器端口
#define SERVERPORT 8877
//缓存大小
#define BUFFSIZE 4096

#endif //TRANSFER_FILE_TRANSFER_H
```

　　然后编写接收端也就是服务器端的程序，具体流程为：创建套接字，配置套接字地址并绑定，监听端口并等待是否有来自客户端的连接请求，建立连接后先接收文件名并创建相应的文件，接收数据并写入文件（*这
里接收和写入的字节数类似于发送端，也需要记录一下*），以下为接收端代码`receive.c`：

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <arpa/inet.h>
#include <netinet/in.h>
#include <sys/socket.h>
#include "transfer.h"

void writefile(int sockfd, FILE *fp);

int main(int argc, char *argv[]) 
{
    //创建TCP套接字
    int sockfd = socket(AF_INET, SOCK_STREAM, 0);
    if (sockfd == -1) 
    {
        perror("Can't allocate sockfd");
        exit(1);
    }
    
    //配置服务器套接字地址
    struct sockaddr_in clientaddr, serveraddr;
    memset(&serveraddr, 0, sizeof(serveraddr));
    serveraddr.sin_family = AF_INET;
    serveraddr.sin_addr.s_addr = htonl(INADDR_ANY);
    serveraddr.sin_port = htons(SERVERPORT);

    //绑定套接字与地址
    if (bind(sockfd, (const struct sockaddr *) &serveraddr, sizeof(serveraddr)) == -1) 
    {
        perror("Bind Error");
        exit(1);
    }

    //转换为监听套接字
    if (listen(sockfd, LINSTENPORT) == -1) 
    {
        perror("Listen Error");
        exit(1);
    }

    //等待连接完成
    socklen_t addrlen = sizeof(clientaddr);
    int connfd = accept(sockfd, (struct sockaddr *) &clientaddr, &addrlen); //已连接套接字
    if (connfd == -1) 
    {
        perror("Connect Error");
        exit(1);
    }
    close(sockfd); //关闭监听套接字

    //获取文件名
    char filename[BUFFSIZE] = {0}; //文件名
    if (recv(connfd, filename, BUFFSIZE, 0) == -1) 
    {
        perror("Can't receive filename");
        exit(1);
    }

    //创建文件
    FILE *fp = fopen(filename, "wb");
    if (fp == NULL) 
    {
        perror("Can't open file");
        exit(1);
    }
    
    //把数据写入文件
    printf("Start receive file: %s from %s\n", filename, inet_ntoa(clientaddr.sin_addr));
    writefile(connfd, fp);
    puts("Receive Success");

    //关闭文件和已连接套接字
    fclose(fp);
    close(connfd);
    return 0;
}

void writefile(int sockfd, FILE *fp)
{
    ssize_t n; //每次接受数据数量
    char buff[MAX_LINE] = {0}; //数据缓存
    while ((n = recv(sockfd, buff, MAX_LINE, 0)) > 0) 
    {
        if (n == -1)
        {
            perror("Receive File Error");
            exit(1);
        }
        
        //将接受的数据写入文件
        if (fwrite(buff, sizeof(char), n, fp) != n)
        {
            perror("Write File Error");
            exit(1);
        }
        memset(buff, 0, MAX_LINE); //清空缓存
    }
}
```
　　项目的makefile:
　　
```make
all : send_file receive_file
.PHONY : all
send_file : send_file.c transfer.h
	gcc -Wall -O2 send_file.c -o send_file
receive_file : receive_file.c transfer.h
	gcc -Wall -O2 receive_file.c -o receive_file
clean :
	rm send_file receive_file
```
　　运行截图：
![发送端](http://i2.muimg.com/588926/408b7d71fe446b25.png)
![接收端](http://i1.piimg.com/588926/3beb3a9199599bf6.png)
