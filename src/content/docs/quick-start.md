---
title: Electron 快速上手：使用HTML、CSS、JavaScript来构建跨平台的桌面应用程序
---

目标是理解 Electron 整个工作流程及三种核心的进程通信方式。

Electron 的核心就是进程通信

## 使用HTML、CSS、JavaScript来构建跨平台的桌面应用程序

HTML、CSS、JavaScript 即通用的 Web 技术。

跨平台指一套代码可以生成windows、linux、macOS三个平台的安装包。

桌面应用程序就是安装在电脑上的那一种应用程序。

## 什么是 Electron

1. 一款应用广泛的跨平台桌面应用开发框架

   应用广泛。指很多软件都有 Electron技术的参与，比如说 VS code、最新版的腾讯QQ等

2. Electron 的本质是结合了 chromium 和 node js

   electron = chromium + node js + 原生API。

   也就是说，首先，我们可以将 HTML、CSS、JavaScript 这些代码交给 chromium 运行，得到一个页面。

   然后，当需要调一些底层的API，比如说在D盘创建一个文件，则需要借助node js。

   native API，叫原生API，比如要修改 windows 操作系统里边某一个注册表里的项，则需要借助 native API。

   学习 Electron 就是学习它提供给我们的这些原生的API。

3. 使用 HTML、CSS、JavaScript 等 Web 技术构建桌面应用程序。

   注意，不一定非得写最为纯粹的 HTML、CSS、JavaScript，也可以写 React、Vue、Less、Sass等，只要能够转成 HTML、CSS、JavaScript 就可以。

# 流程模型

要使用 Electron 写代码，脑子里得有这个流程模型。

Electron 应用的核心围绕着主进程。

主进程就是一个特别纯粹的 js 文件，这个文件基于 node 环境去运行，可以肆无忌惮的调用 node 的任何的API。

主进程最主要的目的是管理渲染进程。

渲染进程可以通过窗口进行理解，每一个窗口背后都对应着一个渲染进程。渲染进程是浏览器环境，可以解析  HTML、CSS、JavaScript。

主进程可以管理多个渲染进程。

这就是主进程和渲染进程之间的关系，所以 Electron 并不是一个单线程的应用。

主进程和渲染进程之间是能通信的（因为环境不同，渲染进程无法执行的操作可以通过通知主进程进行操作）。

主进程可以调用原生API。所谓的原生API，那就是对应windows、linux、macOS 三个平台。原生 API 对三个系统进行的操作（比如系统通知）进行了封装，无需具体调用某一系统的 API 即可实现对应的功能。

##  快速开始（看官网）

npm init 创建 package.json，author 和 description 补全，否则后续无法打包

添加 start 命令：`electron .`

BaseWindow对象实例属性：https://www.electronjs.org/zh/docs/latest/api/base-window

开发者⼯具会报出⼀个安全警告，需要修改 index.html ，配置 CSP(ContentSecurity-Policy) ：`<meta http-equiv="Content-Security-Policy" content="default-src 'self'; style-src 'self' 'unsafe-inline'; img-src 'self' data:;">`

1. default-src 'self' 

   default-src ：配置加载策略，适⽤于所有未在其它指令中明确指定的资源类型。 

   self ：仅允许从同源的资源加载，禁⽌从不受信任的外部来源加载，提⾼安全性。 

2. style-src 'self' 'unsafe-inline' 

   style-src ：指定样式表（CSS）的加载策略。 

   self ：仅允许从同源的资源加载，禁⽌从不受信任的外部来源加载，提⾼安全性。 

   unsafe-inline ：允许在HTML⽂档内使⽤内联样式。 

3. img-src 'self' data: 

   img-src ：指定图像资源的加载策略。 

   self ：表示仅允许从同源加载图像。 

   data: ：允许使⽤ data: URI 来嵌⼊图像。这种URI模式允许将图像数据直接嵌 ⼊到HTML或CSS中，⽽不是通过外部链接引⽤。 

CSP（内容安全策略）：https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CSP

## 完善窗⼝⾏为

Windows 和 Linux 平台窗⼝特点是：关闭所有窗⼝时退出应⽤。

```
// 当所有窗⼝都关闭时
app.on('window-all-closed', () => {
 // 如果所处平台不是mac(darwin)，则退出应⽤。
 if(process.platform !== 'darwin') app.quit()
})
```

mac 应⽤即使在没有打开任何窗⼝的情况下也继续运⾏，并且在没有窗⼝可⽤的情况下激活 应⽤时会打开新的窗⼝。

```
// 当app准备好后，执⾏createWindow创建窗⼝
app.on('ready',()=>{
 createWindow()
 // 当应⽤被激活时
 app.on('activate', () => {
 //如果当前应⽤没有窗⼝，则创建⼀个新的窗⼝
 if (BrowserWindow.getAllWindows().length === 0) createWindow()
 })
})
```

## 配置⾃动重启



## 主进程与渲染进程

主进程只有一个，渲染进程可以有多个

## 预加载脚本

主进程与渲染进程无法直接通信，需要借助预加载脚本

预加载脚本在渲染进程上运行，是浏览器环境，但能访问**部分**Node API

预加载脚本在⽹⻚内容加载之前执⾏

三个脚本的执行顺序：主进程、预加载脚本、渲染进程

## 进程通信

