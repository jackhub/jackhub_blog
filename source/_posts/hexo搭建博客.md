title: hexo搭建博客
date: 2015-01-08 19:42:49
tags: 随笔
---

本片随笔主要记录在使用hexo搭建博客过程中碰到的一些问题点

####1. hexo generate不生成css文件, 造成页面没有加载不了样式
>由于hexo-renderer-stylus是为hexo 3.0所写, 使用旧版本就能解决问题  
>```
$ npm install hexo-renderer-stylus@0.1 --save
```

####2. hexo的配置
>主要配置文件**/blog/_config.yml**和主题文件**/theme_name/_config.yml**  
>参考博客:http://zipperary.com/2013/05/29/hexo-guide-3/

####3. 存放博客图片和一些素材
>在博客目录source中创建一个img文件加, 然后将你要用的图片放在那里, 然后使用/img/image_name引用它就可以啦  
>404页面: 直接在source目录下面创建一个就可以了, 可以使用腾讯公益.

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>404</title>
</head>
<body>
	<script type="text/javascript" src="http://www.qq.com/404/search_children.js" charset="utf-8"></script>
</body>
</html>
```

####4. 使用git保管hexo  

```
#1. 创建一个仓库jackhub_blog, 并clone到本地仓库
$ git clone https://github.com/jackhub/jackhub_blog.git

#2. 加hexo文件复制过来到git目录, 添加并提交
$ git add .
$ git commit -m "blog init"

#3. 同步到git远程仓库中
$ git push

#4. 主题文件中有.git目录,会被当作一个submodule, 是push不到github的, 删除.git目录即可.

#5. 如果已经添加了一个submodule, 可以直接删除这个submodule,使用git rm --cached path_name删除缓存, 重新git add .&& git commit -m "" &&git push
```