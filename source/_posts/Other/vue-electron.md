---
title: "基于 vue + electron 编写一个桌面应用"
date: 2022-06-12 12:53:54
categories:
- 其他
tags:
- 其他
toc: true # 是否显示目录
---
基于 <strong> <span style="color: #ff502c">vue-cli3.0 </strong> + <strong> <span style="color: #ff502c">Electron </strong> 编写一个桌面应用

<!-- more -->

## 「 说明 」
------

项目地址：[Monit](https://github.com/fzf404/Monit)

根据 README.md 文件教程安装依赖并运行

由于对 Electron 暂不熟悉，详细代码实现后续详写，本次先对项目安装构建过程中遇到的几个注意事项记录一下。

##  ⚠️注意事项
------
> * 安装依赖时有些依赖文件较大，需要等待下载一段时间；
> * npm run build 过程中需要安装打包后应用的环境文件等，可能需要80M以上，若不想长时间等待可以使用科学上网或者根据地址手动下载安装包放入文件的方法解决；
> * vue-cli-plugin-electron-builder已集成跨系统打包功能，只需修改打包命令及配置即可实现打包 mac 及 windows 应用；
修改如下
1. vue.config.js 中修改构建选项 builderOptions

```bash
  // 构建选项
  builderOptions: {
    productName: 'Monit', // 应用名
    icon: 'public/logo/icon.png', // 图标
    appId: 'top.fzf404.monit', // app id
    artifactName: '${productName}-${version}-${os}-${arch}.${ext}', // 打包命名方式
    linux: {
      target: 'AppImage',
      publish: ['github'],
    },
    mac: {
      target: {
        target: 'dmg',
        arch: ['x64', 'arm64'],
      },
    },
    win:{ //win相关配置
      "target": [
          {
              "target": "nsis",//利用nsis制作安装程序
              "arch": [
                  "x64",//64位
                  "ia32"//32位
              ]
          }
      ]
    }
  }
```

2. package.json 中修改构建命令

```bash
  "scripts": {
  "serve": "vue-cli-service electron:serve",
  "build": "vue-cli-service electron:build --win --ia32",
  "package-win":"electron-packager ./ --platform=win32 --out ./dist  --overwrite  --ignore=node_modules --arch=x64",
  "release": "vue-cli-service electron:build --publish always",
  "prettier": "prettier --write ."
}
// 不加 --win --ia32 默认打包成当前系统应用
```

参考：
> [基于vue + electron创造一个随心开发组件的跨端桌面应用](https://juejin.cn/post/7103749039677505566)

> [使用vue-cli-plugin-electron-builder开发vue-cli3.0+Electron桌面开发应用](https://www.jianshu.com/p/dfcf2a6a497c)

