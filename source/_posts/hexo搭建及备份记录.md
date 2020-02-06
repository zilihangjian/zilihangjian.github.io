---
title: hexo搭建及备份记录
date: 2020-02-06 10:47:29
author: Excalibur Mogan
img: 
top: true
cover: true
coverImg: 
password: 
toc: false
mathjax: false
summary: 记录步骤及一些错误
categories: Markdown
tags:
  - hexo笔记记录
---
# 博客搭建
在Github创建仓库，本地安装node.js，git和hexo等步骤网上内容非常详尽，就不互相复制了(在此记录多个自用网址)。
* [基本操作](https://www.simon96.online/2018/10/12/hexo-tutorial/)（较为详尽）
* [matery主题设置参考](https://godweiyang.com/2018/04/13/hexo-blog/)（经验证，源代码下载后生成网站有错误，但步骤及设置可以参考）
* [markdown语法相关](https://zealot.top/Hexo-Github%E6%90%AD%E5%BB%BA%E8%87%AA%E5%B7%B1%E7%9A%84%E5%8D%9A%E5%AE%A23.html)
# 主题配置
hexo有很多主题可以设置，较流行的是Next主题，我选了一款比较符合我审美的主题matery，关于主题配置的相关设置，在[matery的github文档](https://github.com/blinkfox/hexo-theme-matery)里有较详细的说明，不在重复叙述。
# 博客备份
由于上传到Github的文件仅仅是生成后的网页文件，在更换电脑或者硬盘损坏（惨痛教训，一度耽误科研进度）导致源文件丢失时并不能通过此文件恢复原始博客文件夹。经过上网搜索，采用github分支备份的方法备份源文件夹。
## 1.新建文件夹存放工作目录
```mkdir hexo```
## 2.新建Github分支hexo（起名自定，已有master分支）
## 3.将Github远程仓库克隆至新建hexo文件夹
```
git clone https://github.com/yourusername/yourusername.github.io hexo
```

**也可用ssh链接，在GitHub仓库页面上即可找到。**

## 4.删除除了版本管理的 .git之外的所有文件和文件夹
```
cd hexo
rm -r *
```
## 5.把要备份的文件复制到hexo目录
```
scaffolds/
source/
themes/
.git/
.gitignore
_config.yml
package.json
```
**这几步是为了得到远程仓库分支后的 .git文件（个人理解）。**

## 6.提交备份
```
git add .		#保存所有文件到暂存区
git commit -m "创建hexo分支"    #提交变更，此次变更名称为“”里的东西
git push --set-upstream origin hexo
#推送到GitHub，并用'--set-upstream'与origin创建关联
#将hexo设置为默认分区
#后续使用可省去'--set-upstream'
```
[参考](https://www.jianshu.com/p/aebeaf050969)（未验证详细步骤，但大体如此）
# 博客编辑
## 插入图片
### 1.在站点配置文件 _config.yml中找到如下代码段并更改（注意和主题配置文件区分）
    post_asset_folder: true		#默认为false，改为true
### 2.在博客根目录下执行命令安装插件
    npm install hexo-asset-image --save
### 3.生成博文
利用`hexo n "博文名"`生成markdown博文时，/source/_posts文件夹里面除了生成一个markdown文件以外还有一个同名文件夹，将要用的图片存放于此即可。博文中插入图片的语法类似于相对路径的表述。
`![pic_alt替代文字](rin.jpg或博文名/图片名.jpg)`
---
**在实际操作过程中发现插入图片显示，原因是此插件有可能会有错误，在生成的html文件中看到图片`<img>`标签链接位置为 .io/文件夹下，正确路径应位于2020/2/6/xxx文件夹下，解决办法如下：
在/yourblogpath/node_modules/hexo-asset-images文件夹下，index.js为其主要代码，对index.js文件中代码进行一些修改。**

```
    var link = data.permalink;
    var beginPos = getPosition(link, '/', 3) + 1;
    var appendLink = '';
    // In hexo 3.1.1, the permalink of "about" page is like ".../about/index.html".
    // if not with index.html endpos = link.lastIndexOf('.') + 1 support hexo-abbrlink
    if(/.*\/index\.html$/.test(link)) {
      // when permalink is end with index.html, for example 2019/02/20/xxtitle/index.html
      // image in xxtitle/ will go to xxtitle/index/
      appendLink = 'index/';
      var endPos = link.lastIndexOf('/');				#将此处“/“改为”.“
    }
    else {
      var endPos = link.lastIndexOf('.');				#将此处”.“改为”/“
    }
    link = link.substring(beginPos, endPos) + '/' + appendLink;
```
具体原理参照[此处博文](https://sonky.top/other/hexo-fix-picture/)
修改以后便可成功插入图片并显示，示例如下：
![fsn第三章](saber.jpg)