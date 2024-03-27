---
title: "Vue2/Vue3优秀开源框架模板"
date: 2024-03-06 21:06:08
categories:
- Vue
tags:
- Vue
toc: true # 是否显示目录
---

> Vue2/Vue3优秀开源框架模板
<!-- more -->

## Vue2
* 花裤衩大佬的 [vue-element-admin](https://github.com/PanJiaChen/vue-element-admin)

## Vue3
* [vue-pure-admin](https://github.com/pure-admin/vue-pure-admin)  🔥 全面ESM+Vue3+Vite+Element-Plus+TypeScript编写的一款后台管理系统（兼容移动端）
* [v3-admin-vite--pany](https://github.com/un-pany/v3-admin-vite/tree/main) ☀️ A vue3 admin template | vue3 admin/element plus admin/vite admin/vue3 template/vue3 后台/vue3 模板/vue3 后台管理系统
* [vue3-element-admin--有来技术](https://github.com/youlaitech/vue3-element-admin) 🔥基于 vue3 + vite5 + typescript + element-plus 构建的后台管理前端模板（配套后端源码），vue-element-admin 的 vue3 版本。
* 基于 Naive UI 的有 `naive-ui-admin` 和 `soybean-admin`
* 基于 Ant Design Vue 的有 `vue-vben-admin` 和 `vue3-antd-admin`
* [fantastic-admin](https://github.com/fantastic-admin/basic)⭐⭐⭐⭐⭐ 一款开箱即用的 Vue 中后台管理系统框架，支持多款 UI 组件库，兼容PC、移动端。vue-admin, vue-element-admin, vue后台, 后台系统, 后台框架, 管理后台, 管理系统

### 问题
vue3-element-admin 项目启动后，vscode命令行提交代码无报错，sourceTree提交报错，pre-commit 报错：
.husky/pre-commit: line 8: npm: command not found
方案：
创建~/.huskyrc文件，根目录/Users/chenzhihao/.huskyrc，增加对nvm路径的支持：
vi ~/.huskyrc
查看npm路径：
which npm
/Users/chenzhihao/.nvm/versions/node/v14.18.1/bin/npm
huskyrc文件修改为：
export PATH=/Users/chenzhihao/.nvm/versions/node/v18.17.0/bin:$PATH
！！！注意对应的版本号
#### 参考
****
https://ask.csdn.net/questions/1058267    
https://blog.csdn.net/wuyulkl/article/details/129803496
https://blog.csdn.net/qq_39852145/article/details/123867238?spm=1001.2101.3001.6650.5&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-5-123867238-blog-131233569.235%5Ev43%5Epc_blog_bottom_relevance_base4&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-5-123867238-blog-131233569.235%5Ev43%5Epc_blog_bottom_relevance_base4&utm_relevant_index=10
## 链接
* [✨ 一个基于 Vue3 + TS + Element Plus + Pinia 的低成本后台管理模板](https://juejin.cn/post/7072633959284703239)