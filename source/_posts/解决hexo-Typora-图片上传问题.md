---
title: 解决hexo + Typora 图片上传问题
date: 2022-01-02 19:05:39
tags: 
- Hexo
---

本文解决hexo + [Typora](https://so.csdn.net/so/search?q=Typora&spm=1001.2101.3001.7020)图片上传不显示的问题

按如下步骤修改设定Typora中图片放置的位置

这样当自己新建一个文章后，如果添加图片，会在文章所在目录下生成一个与文章同名的文件夹，并将图片存入该文件夹中，此后该文章中所有添加的图片均存入该文件夹中。

![image-20231011190708925](%E8%A7%A3%E5%86%B3hexo-Typora-%E5%9B%BE%E7%89%87%E4%B8%8A%E4%BC%A0%E9%97%AE%E9%A2%98/image-20231011190708925.png)

修改博客根目录下的_config.yml文件中的post_asset_folder字段设置为true

当设置 post_asset_folder 参数后，在hexo n命令建立文件时， 会自动建立一个与文章同名的文件夹，把与该文章相关的所有图片资源都放到此文件夹内，这样就可以方便的使用图片资源

同时，只有当post_asset_folder设置为true后，后续安装的插件才会起作用

![image-20231011190829534](%E8%A7%A3%E5%86%B3hexo-Typora-%E5%9B%BE%E7%89%87%E4%B8%8A%E4%BC%A0%E9%97%AE%E9%A2%98/image-20231011190829534.png)

安装插件

到博客的根目录下执行 npm install https://github.com/CodeFalling/hexo-asset-image --save 命令来进行插件的安装

当文章全部写完后，使用Typora的替换功能（替换功能包含删除功能，当替换的内容什么都不输入时为全部删除）将所有图片地址前面多余的部分删除即可
