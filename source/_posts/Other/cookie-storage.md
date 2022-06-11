---
title: "Cookie session localStorage sessionStorage的区别"
date: 2022-03-12 12:22:54
categories:
- Other
tags:
- Other
toc: true # 是否显示目录
---

<!-- more -->

## 一、cookie

* Cookie是服务器发送到用户浏览器并保存在本地的一小块数据。
* 客户端给服务端发送http请求时，服务端会在响应头里面添加一个 Set-Cookie 选项，浏览器收到响应后通常会保存下 Cookie。浏览器得到Cookie之后，每次请求都要带上Cookie
* Cookie会被用户篡改
* 临时Cookie（会话Cookie） 永久Cookie

  不设置过期时间，则表示这个cookie生命周期为浏览器会话期间，只要关闭浏览器窗口，cookie就消失了。
  这种生命期为浏览会话期的cookie被称为会话cookie。会话cookie一般不保存在硬盘上而是保存在内存里。

  设置了过期时间，浏览器就会把cookie保存到硬盘上，
  关闭后再次打开浏览器，这些cookie依然有效直到超过设定的过期时间。

## 二、session
  * 服务器通过cookie给用户一个sessionID，sessionID对应服务器中的一小块内存。
  * 每次用户访问服务器的时候，服务器通过sessionID 读取对应的session，然后获取用户的隐私信息。

## 三、localStorage
  * html5提供的一个API而已
  * 永久有效，除非用户主动清除缓存
  * 设置缓存：localStorage.setItem('key', 'value');// 获取缓存：localStorage.getItem('key');// 清除缓存：localStorage.removeItem('key')// 删除所有保存的数据localStorage.clear();

## 四、sessionStorage
  * sessionStorage用户关闭页面就失效

## 试题1: Cookie 和 Session 的区别
  1. Cookie则是服务器通过set-cookie头给客户端发送的一段字符串，客户端每次访问同个域名都会带上这串字符串
  2. Session是基于Cookie实现的，通过Cookie向客户端发送SessionID，Cookie进行存储。
  3. cookie是浏览器上的hash表，session是服务器上的hash表。
  4. cookie能被篡改，session更安全。

## 试题2: Cookie 和 LocalStorage SessionStorage 的区别
  1. cookie数据大小不能超过4k；sessionStorage和localStorage的存储比cookie大得多，可以达到5M+
  2. cookie设置的过期时间之前一直有效；localStorage永久存储，浏览器关闭后数据不丢失除非主动删除数据；sessionStorage数据在当前浏览器窗口关闭后自动删除
  3. cookie的数据会自动的传递到服务器；sessionStorage和localStorage数据保存在本地