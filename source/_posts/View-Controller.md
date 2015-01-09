title: View Controller
date: 2015-01-09 14:48:51
tags: iOS
---
###ViewController的结构关系
* ViewController中包含一个指向storyboard的指针, 一个项目中只有一个storyboard, 所有的其他viewController也都指向它.
* viewController中包含一个UIview的指针, 它是这个viewCntroller的根视图, 被viewControl管理的其他视图都在它之上.
* viewControl来负责实现它所管理的视图的响应方式, 这个实现了显示和控制的隔离.

![viewController](http://i2.tietuku.com/aa8b64d36df89717.png)

###ViewControl与windows的关系
对下面的图片解释一下:  

* window从screen那里获取屏幕的显示信息,根据此初始化UIWindow frame大小.
* UIWindow中包含有一个*rootViewController的指针, 我们让它指向我们的viewController, 这样我们viewControl中的根UIview会自动添加为UIWindow的subView, viewController中的内容就能显示在窗口中了.


![viewWindows](http://i2.tietuku.com/5def40f7afd0c98b.png)