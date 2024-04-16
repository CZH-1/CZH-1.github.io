---
title: "部署vue项目至服务器"
date: 2022-03-12 12:22:54
categories:
- 其他
tags:
- 其他
toc: true # 是否显示目录
---

> 部署vue项目至服务器 

<!-- more -->

# 步骤如下

1. 下载 [finalShell](https://www.hostbuf.com/)

2. 点击文件夹点击ssh连接

  * 名称 自定义
  * 主机 服务器公网ip
  * 连接名 root
  * 密码 服务器登记密码
  * 点击确认 接受并保存 进入命令界面
3. 安装PCRE pcre-devel 和Zlib

4. 可能还需安装GCC和OpenSSL

5. 安装 Nginx
  * 版本不对可能会报错（可以通过注释 -Wrong 或者 换版本 解决）
  * 目前是 1.16.1

    ```bash
    # 下载
    [root@iZbp1e31bqkj6opg1bsdr0Z ~]# wget -c https://nginx.org/download/nginx-1.14.0.tar.gz
    # 解压并进入nginx目录
    [root@iZbp1e31bqkj6opg1bsdr0Z ~]# tar -zxvf nginx-1.14.0.tar.gz
    [root@iZbp1e31bqkj6opg1bsdr0Z ~]# cd nginx-1.14.0
    # 使用nginx的默认配置, 编译安装
    ./configure
    make
    make install
    # 查找nginx位置
    [root@iZbp1e31bqkj6opg1bsdr0Z ~]# whereis nginx
    # 进入 nginx sbin 文件下启动
    [root@iZbp1e31bqkj6opg1bsdr0Z ~]# cd /usr/local/nginx
    [root@iZbp1e31bqkj6opg1bsdr0Z nginx]# cd sbin/
    [root@iZbp1e31bqkj6opg1bsdr0Z sbin]# ./nginx
    # 查看启动状态
    [root@iZbp1e31bqkj6opg1bsdr0Z sbin]# ps -ef|grep nginx
    # root      600651       1  0 13:57 ?        00:00:00 nginx: master process ./nginx
    # nobody    600652  600651  0 13:57 ?        00:00:00 nginx: worker process
    # root      665038  658796  0 14:35 pts/0    00:00:00 grep --color=auto nginx
    # 验证你的nginx.conf文件是否是正确的, 文件位置 /usr/local/nginx/conf
    [root@iZbp1e31bqkj6opg1bsdr0Z sbin]# ./nginx -t
    # nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
    # nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
    # 修改nginx.conf文件
    [root@iZbp1e31bqkj6opg1bsdr0Z nginx]# cd conf
    [root@iZbp1e31bqkj6opg1bsdr0Z conf]# vim /nginx.conf
    # 重启 nginx
    [root@iZbp1e31bqkj6opg1bsdr0Z sbin]# ./nginx -s reload
    
    ```
6. 浏览器输入服务器IP(公网IP)即可看到页面
  * 若nginx启动成功，浏览器无法访问，可能是防火墙开了
  * 服务器端口配置的不与.conf中一致（以我为例：服务器是22，.conf为80），修改conf或者服务器端口设置（阿里云服务器可设置一段区间都可访问）

7. 打包个人vue项目
  * 打包配置可能需设置

8. 将dist文件放入nginx目录下
  * finalShell 无法直接上传文件夹，只能单文件（文档写的是支持的，可能是操作问题）
  * 因为第一步的问题，所以我手动新建 dist 文件夹，再把 vue 打包的dist文件中单个文件共同上传至该文件夹中

9. 修改.conf中访问文件的目录
  ```bash
  location / {
    # root后默认是html
    root /usr/local/nginx/dist;
    index index.html index.html;
  }
  
  注意：npm run bulid 中，设置 
  vue2： publicPath : process.env.NODE_ENV === 'production'  ? "/meal"  : "/"
  })，生成的路径应带上文件根目录地址
  vue3: base: './'  生成 相对路径 ./assets/ (vite配置：https://blog.csdn.net/weixin_45822171/article/details/127275984)
  上述 /meal 生成路径 /meal/assets/ 打包之后index.html中路径如下：(若不加/meal,上传至服务器之前，无法访问)
  <!doctype html>
  <html lang="">
  <head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1">
  <link rel="icon" href="/meal/favicon.ico">
  <title>today-meal</title>
  <script defer="defer" src="/meal/js/chunk-vendors.0833686f.js"></script>
  <script defer="defer" src="/meal/js/app.31f1556d.js"></script>
  <link href="/meal/css/chunk-vendors.e18646e2.css" rel="stylesheet">
  <link href="/meal/css/app.8bd79ca3.css" rel="stylesheet">
  </head>
  <body>
  <noscript><strong>We're sorry but today-meal doesn't work properly without JavaScript enabled. Please enable it to continue.</strong></noscript><div id="app"></div><
  /body>
  </html>
  
   location /meal {
              root   /usr/local/nginx;  
              index  index.html index.htm;
          }
    http://web.czhhx.cn/neck === http://web.czhhx.cn/neck/index.html
  
   location /neck {
              root   /usr/local/nginx;
              try_files $uri $uri/ /index.html;  // 前端history模式404问题：当用户刷新页面时，Nginx会先检查当前URL是否存在，如果不存在，就会尝试访问index.html，从而可以正常显示页面。
              index  index.html index.htm;
          }
  location /refresh-step/ {
      proxy_pass  http://127.0.0.1:7001; # 转发规则
      proxy_set_header Host $proxy_host; # 修改转发请求头，让8080端口的应用可以受到真实的请求
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
   
    meal: /usr/local/nginx/meal
    refresh-steps: /server/refresh-steps
  ```
10. 重启nginx [root@iZbp1e31bqkj6opg1bsdr0Z sbin]# ./nginx -s reload

11. 访问浏览器即可看到vue项目

# 完结

[linux服务器安装nginx以及vue项目打包部署(Mac)](https://www.jianshu.com/p/16df34e5d9ff)
[nginx配置](https://blog.csdn.net/weixin_42329623/article/details/134527035)