title: View Program
date: 2015-01-08 18:47:32
tags: iOS
---

###The view draw cycle 『view生命周期』
When a view first appears on the screen, the system asks it to draw its content. The system captures a snapshot of this content and uses that snapshot as the view’s visual representation. If you never change the view’s content, the view’s drawing
code may never be called again. The snapshot image is reused for most operations involving the view. If you do change the content, you notify the system that the view has changed. The view then repeats the process of drawing the view and capturing a snapshot of the new results.

当你的view内容变化的时候，我们不直接重绘它，而是通过*setNeedsDisplay*和*setNeedsDisplayInRect:*方法通知系统去帮我们完成重绘。
>When the contents of your view change, you do not redraw those changes directly. Instead, you invalidate the view using either the setNeedsDisplay or setNeedsDisplayInRect: method.  

###Content Mode 『如何拉伸属性』

设置当view的形状发生变化时，它的content如何重绘，例如图片是拉伸，平铺等。
>Each view has a content mode that controls how the view recycles its content in response to changes in the view’s geometry and whether it recycles its content at all.  

一般有这些类型  

* UIViewContentModeScaleAspectFill		*【满屏-不变形】*
* UIViewContentModeScaleAspectFit		*【适屏-不变形】*
* UIViewContentModeScaleToFill			*【拉到铺满-会变形】*

你也可以使用自己定义的**drawRect：**方法来自定义，此时应该设置Contenct Mode为：  

* UIViewContentModeRedraw  

###Built-In Animation Support『动画』
要使用动画，只需两个步骤  

* 告诉UIKit你希望执行动画
* 改变属性的值
>1. Tell UIKit that you want to perform an animation.
>2. Change the value of the property.  

```
#例子为某个viewController中的方法，self.view为视图控制器根式图
#告诉UIKit你希望执行动画
[UIView beginAnimations:@"View Flip" context:NULL];
[UIView setAnimationDuration:0.4];
[UIView setAnimationCurve:UIViewAnimationCurveEaseInOut];
...other codes
#在指定的view中假如动画
[UIView setAnimationTransition:UIViewAnimationTransitionFlipFromLeft forView:self.view cache:YES];
...other codes
#提交动画
[UIView commitAnimations];

```

<!--more-->
###View Geometry and Coordinate Systems 『坐标系统』


The Relationship of the Frame, Bounds, and Center Properties  
A view object tracks its size and location using its frame, bounds, and center properties:  
● The **frame** property contains the frame rectangle, which specifies the size and location of the view in its ***superview’s coordinate system.『frame是相对父视图的坐标系统』***  
● The **bounds** property contains the bounds rectangle, which specifies the size of the view (and its content origin) in the view’s own ***local coordinate system.『bounds是相对自身坐标系统 』***   
● The center property contains the known center point of the view in the ***superview’s coordinate system.『center也是相对父视图坐标系统』***  

更改view的形状和坐标值，一边我们使用Frame和Center属性。
>You use the center and frame properties primarily for manipulating the geometry of the current view.If you are changing only the position of the view (and not its size), the center property is the preferred way to do so.

Bounds就是定义了在view自身中的一个区域, 在这个区域内画的图形才是可见的.
>You use the bounds property primarily during drawing. The bounds rectangle is expressed in the view’s own local coordinate system. The default origin of this rectangle is (0, 0) and its size matches the size of the frame rectangle. Anything you draw inside this rectangle is part of the view’s visible content.

###Points Versus Pixels 『坐标与像素』

在iOS中的所有坐标值和距离都是用浮点数坐标Points表示的.

>Device Screen dimensions (in points) 
> 
iPhone and iPod touch devices with 4-inch Retina display **320 x 568**  
Other iPhone and iPod touch devices **320 x 480**  
iPad **768 x 1024**  

##Windows『窗口』
每个iOS程序至少需要一个窗口(UIWindow类), 有些可能程序可能需要多个. 一个window主要负责这些事项:
一个提供显示的区域, 传递事件和与view controller一起为屏幕旋转提供支持.

Every iOS application needs at least one window—an instance of the UIWindow class—and some may include more than one window. A window object has several responsibilities:  
  
*  It contains your application’s visible content.    
*  It plays a key role in the delivery of touch events to your views and other application objects.  
*  It works with your application’s view controllers to facilitate orientation changes.  

>In iOS, windows do not have title bars, close boxes, or any other visual adornments. A window is always just a blank container for one or more views. Also, applications do not change their content by showing new windows. When you want to change the displayed content, you change the frontmost views of your window instead.

###Creating a Window Programmatically 『创建窗口』
通过代码创建窗口

```
self.window = [[[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]]
autorelease];
...
[self.window makeKeyAndVisible];
```
###Adding Content to Your Window 『添加content到窗口』

通常每个window都有一个root view对象, 它包含所有其他views并显示内容. 这样的话如果你需要更新窗口中的内容, 你仅需要替换root view就可以完成. 安装一个view到你的window中, 使用**addSubview:**方法. 例如安装一个由view controller管理的视图:

>Each window typically has a single root view object (managed by a corresponding view controller) that contains all of the other views representing your content. Using a single root view simplifies the process of changing your interface; to display new content, all you have to do is replace the root view. To install a view in your
window, use the addSubview: method. For example, to install a view that is managed by a view controller, you would use code similar to the following: 
 
```
[window addSubview:viewController.view];
```

如果在nib设置中设置了root viewController, 当window加载时, UIKit将自动安装root viewController中的view到window中.

>If the root view of your window is provided by a container view controller (such as a **tabbar controller, navigation controller, or split-view controller**), you do not need to set the initial size of the view yourself. The container view controller automatically sizes its view appropriately based on whether the status bar is visible.

###Monitoring Window Changes 『监视窗口变化』
If you want to track the appearance or disappearance of windows inside your application, you can do so using these window-related notifications:  

* UIWindowDidBecomeVisibleNotification
* UIWindowDidBecomeHiddenNotification  
* UIWindowDidBecomeKeyNotification  
* UIWindowDidResignKeyNotification  

##View 『视图』

由于view是你的程序中与用户最主要的交互的交互方式, view有非常多的功能, 例如下面这些:
   
* Layout and subview management
	* A view defines its own default resizing behaviors in relation to its parent view.
	* A view can manage a list of subviews.
	* A view can override the size and position of its subviews as needed.
	* A view can convert points in its coordinate system to the coordinate systems of other views or the
window.
* Drawing and animation
	* A view draws content in its rectangular area.
	* Some view properties can be animated to new values.
* Event handling
	* A view can receive touch events.
	* A view participates in the responder chain.

###Creating and Configuring View Objects『创建和设置视图』

手动在程序中创建一个view:  
  
```
CGRect viewRect = CGRectMake(0, 0, 100, 100);       
UIView* myView = [[UIView alloc] initWithFrame:viewRect];
```  

###Adding and Removing Subviews 『视图层次管理方法』
主要有下面这些方法:

* addSubview:
* insertSubview:...
* bringSubviewToFront:
* sendSubviewToBack:
* exchangeSubviewAtIndex:withSubviewAtIndex:
* removeFromSuperview:

注意: 如果subview的frame超过了superview的bounds, 默认超出的部分是不会被裁剪掉的,就是会被显示出来,如果需要被裁剪,设置superview中的clipsToBounds属性为YES即可.
>A subview whose frame lies outside of its superview’s visible bounds is not clipped by default. If you want your subview to be clipped to the superview’s bounds, you must explicitly set the clipsToBounds property of the superview to YES.

###Locating Views in a View Hierarchy 『定位视图Tag』
在view hirearchy中获取一个view, 通过如下方法:

* 存一个指向这个view的指针
* 给view标识一个tag, 通过**viewWithTag:**方法获取它

```
#通过tag属性设置tag值
#@property(nonatomic) NSInteger tag
myView.tag=100;
#通过viewWithTag：方法获取指定tag值的view
UIView* herView=[self.window viewWithTag:100]

```


>There are two ways to locate views in a view hierarchy:
>  
* Store pointers to any relevant views in an appropriate location, such as in the view controller that owns the views.  
* Assign a unique integer to each view’s tag property and use the viewWithTag: method to locate it.

###Translating, Scaling, and Rotating Views『平移缩放和旋转』
通过view中的transform属性来旋转  

```
#对视图比例缩放
CGAffineTransform CGAffineTransformScale ( CGAffineTransform t, CGFloat sx, CGFloat sy );
#对视图做变焦旋转
CGAffineTransform CGAffineTransformRotate ( CGAffineTransform t, CGFloat angle );
#对视图做平移
CGAffineTransform CGAffineTransformConcat ( CGAffineTransform t1, CGAffineTransform t2 );

# M_PI/4.0 is one quarter of a half circle, or 45 degrees.    
CGAffineTransform xform = CGAffineTransformMakeRotation(M_PI/4.0);  
self.view.transform = xform;  
```

###Converting Coordinates in the View Hierarchy『坐标系统转换』
坐标转换

convertPoint:fromView:  
convertRect:fromView:  
convertPoint:toView:  
convertRect:toView:  
convertPoint:fromWindow:  
convertRect:fromWindow:  
convertPoint:toWindow:  
convertRect:toWindow:  
