---
title: "jimmysong.io change log"
date: 2017-10-04T11:18:27+08:00
draft: false
categories: github
tags: ["github-pages"]
---

### TODO

- [x] 移动设备页面适配问题，手机中打开有些长代码页面会需要缩放
- [ ] 评论框一直是个问题，现在使用的 `Gitment` 当文章的 URL 改变后需要重新 initial
- [x] 每篇文章的标题显示还需要优化，对齐方式问题
- [ ] ⚠️文章正文部分的如果在 `li` 中有超长的使用点号连接起来的长“词”将无法实现分词，将导致移动设备上显示有问题
- [ ] 在主页中增加受欢迎文章和最新文章的入口，丰富首页功能
- [x] 增加主页的个性化banner，并添加 description
- [x] 增加搜索框，使用 [algolia](https://www.algolia.com/doc/tutorials/search-ui/instant-search/build-an-instant-search-results-page/instantsearchjs/)
- [x] 根据文章需求确定是否显示评论框
- [ ] 搜索框中的URL高亮标签去除

### 2017-03-17

1. 我的博客 http://rootsongjc.github.io 上线，使用GitHub pages 托管
2. 使用 Steve Francia 提供的 Nixon 主题
3. 图片保存到七牛云存储
4. 使用百度统计

### 2017-08-18

1. 在 [namecheap](https://namecheap.com) 注册独立域名 jimmysong.io

### 2017-09-03

1. 使用 [cloudflare](https://www.cloudflare.com/) 增加 https 支持
2. 不再使用七牛云存储，直接用GitHub存储图片

### 2017-10-01

1. 替换了原来的 [nixon](https://themes.gohugo.io/nixon/) 主题，使用新的 [Beautiful Hugo](https://themes.gohugo.io/beautifulhugo/) 主题
2. 增加 **categories**，为之前写的所有文章分门别类，并在主页上使用下拉菜单导航
3. 设计了网站的主页，明确网站的 topic 为 **Building Cloud Native Confidence**
4. 使用了 menu 导航，快速进入相关文章列表，暂时规划为如下几个类别：
   - Kubernetes：所有与 kubernetes 相关的文章
   - Cloud Native：非 kubernetes 技术本身的与云原生相关的内容，如 service mesh
   - Container：容器生态、工具和开发
   - Github：开源项目、GitHub Pages、本博客相关的内容
   - DevOps：CI、CD、流程自动化
   - Code：程序设计、代码、编程语言相关
   - Architecture：软件架构
5. 在导航栏中增加 **Books** 和 **Projects** 内容，为我的书籍和开源项目导航
6. 优化了文章的标签，并提供标签导航页面
7. 使用 CDN，优化了网站的加载速度
8. 删除原有的划分不合理的 `blogs`、`talks`、`projetcs` 路径，所有文章都从 `posts` 路径直接访问
9. 页面上终于可以看到我的 logo 了，Jimminetes、Kubesong 😂
10. 将默认的代码高亮插件 highlight 替换为 [prism](http://prismjs.com/download.html)

### 2017-10-02

1. 修复了移动设备适配问题，仅仅在 `main.css` 中增加了一行代码而已。

### 2017-10-03

1. 修改文章的标题对齐方式为居中对齐
2. 在文章标题下方增加字数统计、阅读时间建议、文章创建时间记录
3. 给网站首页增加多张图片做动态banner
4. 自定义了hello variation为"Hi,I'm Jimmy"，并修改了subtitle
5. 重新规划了导航栏的下拉菜单顺序

### 2017-10-04

1. 修改下拉菜单、超链接、tag等的配色，使用蓝色系

### 2017-10-07

1. ~~不再使用 maxcdn，切换为 [bootcdn](http://www.bootcdn.cn/)|又拍云，加快国内网站速度更快~~，但是用了这个CDN国外用户访问的速度将大大受影响，有的地方网页基本打不开，退而求其次，继续使用maxcdn
2. 主页 banner 图片使用 [Cloudinary](https://cloudinary.com/) 存储
3. 全站https化，将原来在七牛云存储的图片转移到了Cloudinary

### 2017-10-08

1. 增加文章头部metadata中的`nocomment`标记，如果有此标记则不显示评论框
2. 增加搜索框