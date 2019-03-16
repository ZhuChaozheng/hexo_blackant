#实验室博客使用文档

***

##需要了解的内容：
git/hexo/nodejs/（markdown语法/html+js+css）


##从零开始
详见<https://www.jianshu.com/p/f4cc5866946b>
*注意：从零开始搭博客，`git init`之后的CNAME文件需要改成 blog.blackant.org*

##弹射起步(在git/hexo都安装好的情况下,但不需要在本地初始化hexo(hexo init)，依赖要安装)

1.克隆实验室博客源码

```
git init
git clone https://github.com/ZhuChaozheng/hexo_blackant.git

```

2.在本地查看博客内容：
```
hexo gengrate/hexo g
hexo server/hexo s
```
3.修改博客

进入/source/_posts文件夹，对其中的md文件进行创建/修改

4部署到自己的github上查看效果
安装git插件：
`npm install hexo-deployer-git --save`
部署到github上
`hexo deploy`
*注意：上传的github仓库名一定要是usernanme.github.io,username是你的Git的用户名*

网页输入<usernanme.github.io>查看博客效果，不同于之前的`hexo g/hexo s`,也可以将网址发给别人看到博客的内容

5合并自己与实验室git的分支
详见<https://blog.csdn.net/javagaorui5944/article/details/52976365>
*注意：需要分别将主目录下的内容与public文档的内容上传到<https://github.com/ZhuChaozheng/hexo_blackant>与<https://github.com/ZhuChaozheng/ZhuChaozheng.github.io>*

