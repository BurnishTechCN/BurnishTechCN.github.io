# Burnish team blog

## 使用 Hexo
具体的可以看这里：[使用Hexo写博客 | Burnish](http://blog.burnish.cn/posts/use-Hexo/)

下面是简略版的

```
# 生成新的markdown文件，在source/_post/文件夹下
hexo new "your title"

# 写博客，markdown语法
vim source/_post/your title.md

# 预览
hexo server

# 生成博客
hexo generate

# 发布（发布之前要先 generate）
hexo deploy

# 将代码push到source分支
git push origin source:source
```

## 注意事项
* 使用 `hexo new "title"` 生成一个新的文件后，打开它会有一些基础信息，你需要完善它

```
# title 是文章标题，默认是你 hexo new 写的那个
title: npm 私有仓库搭建

# date 是自动生成的，不用管
date: 2016-07-26 13:38:31

# permalink 是文章的链接，默认是空，写文章的时候一定要填上
# 格式：用标题的英文翻译，空格用 - 代替
permalink: build-private-npm-repository-server

# 标签
tags:
---
```

* `<!-- more -->` 可以自定义文章在首页要展示多少内容，`<!-- more -->` 之后的内容会显示 `阅读更多`

* 在发布之前先 `pull` 远端最新的代码后再使用 `hexo server` 预览一下再发布
