#实验室博客使用文档

***

##需要了解的内容：
git/hexo/nodejs/（markdown语法/html+js+css）


##从零开始
详见<https://www.jianshu.com/p/f4cc5866946b>
*注意：从零开始搭博客，`git init`之后的CNAME文件需要改成 blog.blackant.org  且为了支持latex公式，其渲染引擎需要更换*

##弹射起步(在git/hexo都安装好的情况下,但不需要在本地初始化hexo(hexo init)，依赖要安装)
1. 创建文件夹('blog'为文件夹名字)
`mkdir blog`

2. 克隆实验室博客源码

```
cd blog
git init
git clone https://github.com/ZhuChaozheng/hexo_blackant.git

```

3. 在本地查看博客内容：
```
hexo gengrate/hexo g
hexo server/hexo s
```
*有关latex公式，可能会出现在本地查看部分公式有误差，但是部署到github上却能够实现的情况，以github上的为准*

4. 修改/创建文章

进入/source/_posts文件夹，对其中的md文件进行创建/修改，语法见*markdown*语法

5. 部署到自己的github上查看效果
安装git插件：
`npm install hexo-deployer-git --save`
部署到github上
`hexo deploy`
*注意：上传的github仓库名一定要是usernanme.github.io,username是你的Git的用户名*

网页输入<usernanme.github.io>查看博客效果，不同于之前的`hexo g&&hexo s`,也可以将网址发给别人看到博客的内容

5合并自己与实验室github的分支
详见<https://blog.csdn.net/javagaorui5944/article/details/52976365>
*注意：需要分别将主目录下的内容与public文档的内容上传到<https://github.com/ZhuChaozheng/hexo_blackant>与<https://github.com/ZhuChaozheng/ZhuChaozheng.github.io>*

