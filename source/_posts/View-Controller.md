title: View Controller
date: 2015-01-09 14:48:51
tags: iOS
---
###ViewController的结构关系
* ViewController中包含一个指向storyboard的指针, 一个项目中只有一个storyboard或者没有, 所有的其他viewController也都指向它.
* viewController中包含一个UIview的指针, 它是这个viewCntroller的根视图, 被viewControl管理的其他视图都在它之上.
* viewControl来负责实现它所管理的视图的响应方式, 这个实现了显示和控制的隔离.

![viewController](http://i2.tietuku.com/aa8b64d36df89717.png)

###ViewControl与windows的关系
对下面的图片解释一下:  

* window从screen那里获取屏幕的显示信息,根据此初始化UIWindow frame大小.
* UIWindow中包含有一个*rootViewController的指针, 我们让它指向我们的viewController, 这样我们viewControl中的根UIview会自动添加为UIWindow的subView, viewController中的内容就能显示在窗口中了.

> A view controller is usually owned by a window or another view controller.If a view controller is owned by a window object, it acts as the window’s root view controller.The view controller’s root view is added as a subview of the window and resized to fill the window.If the view controller is owned by another view controller, then the parent view controller determines when and how the child view controller’s contents are displayed.

<!--more-->
![viewWindows](http://i2.tietuku.com/5def40f7afd0c98b.png)

##演示4中不同的视图控制实现方式
* 完全手动编写ViewControl
* 不使用ViewController
* 使用nib文件初始化viewController
* 使用storyboard管理ViewController

![viewController](/img/viewController.png)


###不使用ViewController
* 在AppDelegate中手动完成显示『View』和控制『Controller』

```
//@implementation AppDelegate
//显示『View』
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
// Override point for customization after application launch.
    self.window=[[UIWindow alloc]initWithFrame:[[UIScreen mainScreen] bounds]];
    self.window.backgroundColor=[UIColor grayColor];   
//contentMode
    UIImageView *iv=[[UIImageView alloc]initWithImage:image];
    iv.tag=10;
    iv.contentMode=UIViewContentModeScaleAspectFit;
    iv.backgroundColor=[UIColor brownColor];
    iv.frame=CGRectMake(30, 350, 260, 100);
//Affine
    UIButton *press=[[UIButton alloc] initWithFrame:CGRectMake(200, 500, 40, 10)];
    press.backgroundColor=[UIColor yellowColor];
    press.titleLabel.text=@"Press me";
    [press addTarget:self action:@selector(change) forControlEvents:UIControlEventTouchUpInside];
    
    [self.window addSubview:iv];
    [self.window addSubview:press];
    [self.window makeKeyAndVisible];
    
    return YES;
}
//控制『Controller』
-(void) change {
    [UIView beginAnimations:@"chang" context:nil];
    [UIView setAnimationDuration:1];
    [UIView setAnimationCurve:UIViewAnimationCurveEaseInOut];
    
    UIView *view=[self.window viewWithTag:101]; //通过tag获取view
    view.transform=CGAffineTransformRotate(view.transform,M_PI/4.0);
    
    [UIView setAnimationTransition:UIViewAnimationTransitionFlipFromLeft forView:view cache:YES];
    
    [UIView commitAnimations];
    
    
}
```


###完全手动编写ViewControl
* 新建项目，修改xcode项目中deployment info中的信息，将『Main Interface』置成空，删除其对应的***Main.Stroyboard***，因为我们现在不使用storyboard。
* 将deployment info中的Launch Screen File置成空，并删除其对应的文件***LanuchScreen.xib***，这里也不需要启动程序初始化界面。
* 补充AppDelegate代码段

```
//@implementation AppDelegate
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
// Override point for customization after application launch.
    self.window=[[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
    ViewController *rootcontroller=[[ViewController alloc]init];
    [self.window addSubview:rootcontroller.view];
    [self.window makeKeyAndVisible];
    return YES;
}
```
* 在ViewController类中手动添加视图代码，在视图控制器view被加载时调用。

```
//@implementation ViewController
//视图『View』
- (void)viewDidLoad {
    [super viewDidLoad];
// Do any additional setup after loading the view, typically from a nib.
    UILabel* label=[[UILabel alloc]initWithFrame:CGRectMake(50, 200, 300, 20)];
    label.text=@"This ViewController init by code";
    UIButton* button=[UIButton buttonWithType:UIButtonTypeSystem];
    button.frame=CGRectMake(100, 400, 40, 20);
    button.titleLabel.text=@"Press me";
    button.backgroundColor=[UIColor blueColor];
    [button addTarget:self action:@selector(press) forControlEvents:UIControlEventTouchUpInside];
    [self.view addSubview:label];
    [self.view addSubview:button];
    self.view.backgroundColor=[UIColor yellowColor];
}
//控制『Controller』
-(void)press {
    self.view.backgroundColor=[UIColor greenColor];
}
```


###使用nib文件初始化viewController

* 新建项目，修改xcode项目中deployment info中的信息，将『Main Interface』置成空，删除其对应的***Main.Stroyboard***，因为我们现在不使用storyboard。
* 将deployment info中的Launch Screen File置成空，并删除其对应的文件***LanuchScreen.xib***，这里也不需要启动程序初始化界面。
* 创建试图控制器类『ViewController』以及其对应的xib文件『ViewController.xib』,并在属性界面『identify』中添加关联，将xib文件与ViewController关联起来，然后在『connections』属性中的view拉一根线指向xib文件的视图区域。
* 现在就可以在『ViewController.xib』文件中添加视图空间，完成界面设计了。
* 补充AppDelegate中的启动初始化代码，如下：

```
//@implementation AppDelegate
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    self.window=[[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
    ViewController *rootcontroller=[[ViewController alloc]initWithNibName:@"ViewController" bundle:nil];
    //------------*
    self.window.rootViewController=rootcontroller;
    //rootcontroller的view会被自动添加为self.window的subview，被会自动根据screen的值调整view画布大小
    //--上面这一段也可以用下面的表示
    //[self.window addSubview:rootcontroller.view];
    //------------*
    [self.window makeKeyAndVisible];
    return YES;
}
```
* 在ViewController.m中完成完成控制代码，这个我们经常做，从控件拉一条剪头到ViewControler.m中，并实现，xcode自动完成UIControl与方法的绑定『-addTarget:action:forControlEvents:』。


>在使用nib文件时，启动时将自动从nib文件中加载并**生产相应的视图代码段**，供给viewController使用，而且我发现当通过nib文件初始化界面后一次，后面即使你删掉这个nib文件，视图依旧能正常加载并工作。

###使用storyboard管理ViewController

现在开始学iOS的同学，估计一开始就是直接用storyboard，相当方便。连上面AppDelegate部分的代码都不需要写了，下面说一下要使用storyboard相比上面要设置哪些东西。  

* 由于使用storyboard需要xcode自动去帮我们完成试图控器的加载，我们需要在『项目名』->『Deployment info』->『Main Interface』属性处设置你项目中创建的storyboard的名称。
* 其次，在你的众多或者唯一的viewController『attributes inspector』属性中设置为『is initial View Contr』


