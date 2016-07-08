最近**Pokemon Go** 火的不行，结果人家服务器扛不住了，只接受GPS定位信息在澳洲的客户端的登陆请求。今天自己搞了个小Demo，在未越狱的iPhone上把系统的GPS定位信息给改了。
#### 步骤:
在`Xcode8-beta`上新建一个空白工程 ->  `Edit scheme` -> `Default Location` 选澳洲 -> Device选真机设备，Run
 ![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1126909-f81a3fe8d9412d34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


现在，看看自己手机(我是iOS10系统)：

 1.微信：

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1126909-0783e5217d2f49e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.高德地图：

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1126909-9c06a70e07fd5bf7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3.系统相机随便拍个照，查看位置信息

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1126909-73cc4236d81e4cb0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4.查找我的iPhone

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1126909-71330d8552750469.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在美中不足是，即使应用删除，GPS定位信息还是假的,需要删除应用重启手机，才能恢复正常GPS。Demo我放在了Github上。

早有Fake GPS这种提供虚拟定位信息的应用。劳资以前没有研究过.也有人逆向微博，在运行时[修改定位信](http://my.oschina.net/rockee123/blog/114518). 但这些方法的前提是你有个越狱设备。Quora上早也有讨论过，[如何未越狱设备上如何修改GPS定位信息](https://www.quora.com/How-can-I-fake-my-iPhone-location-without-jailbreaking).各路神招:重载CLLocationManager方法、安装GPS中继器假冒GPS信号、设备代理、iOS模拟器Debug模式的Location模拟......

----

#### GPS定位原理
我们先来看一个博客，[iOS定位原理](http://m.blog.csdn.net/article/details?id=40080533&from=singlemessage&isappinstalled=0).我抄了一段：

```
GPS定位原理:
	这个是美国军方搞的, 军民两用, 但仍然完全受军方控制(比如可以随时随地设置某个地区收到的信号与否及其精度), 其原理就是:利用天上的卫星(共24颗)不断地广播信号, 地面的GPS接收设备收到信号后, 通过分析多个卫星信号，就可以计算出地球坐标, GPS保证全球任何一个地方(98%)都可以同时收到至少4个卫星的信号, 从而可以准确确定您的经纬度以及海拔位置. GPS定位精度可达10米以内, 不过这是美国军方控制的, 战争时期可能变的不稳定或者误报. (不过我国也有自己的系统, 北斗系统, 目前已经亚洲的覆盖, 还在推广中)

卫星定位必须要能收到卫星信号，因为卫星信号都是很微弱的，虽然iPhone 4开始提升了GPS硬件的灵敏度，但仍然需要在窗户旁，或者户外使用，才能确保卫星信号的强度和稳定性。

与基站定位和WIFI定位相比，GPS耗电最大，速度最慢，但是精度最高。

而iPhone的GPS与纯粹的GPS定位不同, 称为A-GPS, 即辅助GPS.(比GPS更优，不过GPS也分芯片和性能的，不是说所有的AGPS比所有的GPS都好)

因为GPS定位中最耗时、最耗电的就是获取当前天上的卫星信息，哪些卫星可见、在什么位置、时钟是多少等等，这个过程可能花费数十秒甚至几分钟，而AGPS就是利用网络，首先将基站定位或者WIFI定位获得的大概位置发到远程服务器，有服务器进行查询和计算，得出这个位置下当前卫星信息，反馈给iOS设备，iOS设备就可以直接用这些信息来接受卫星信号，不用自己去扫描分析了。这样可以极大提高定位速度，将初次定位时间缩短到1~2秒完成。

A-GPS优点是定位快，缺点是需要网络，但也只是在初次定位时需要网络，因为一旦卫星信息返回，在有限时间和范围内，这些信息无须改变，之后的GPS定位就不再需要联网，都是直接用这些卫星参数接受信息了。

在iOS设备上，上述定位方式会综合应用，一般地，可能先按照最快的“无网基站定位”返回一个位置，当有网络连接时，在用有网基站定位更新位置，然后，利用AGPS上网查询卫星星图，最后，在能收到GPS信号的情况下，转为使用GPS定位。

根据当前信号情况和网络环境，iOS可能在上述方式之间反复迭代，不一定一致特定步骤或者方式，而且随着iOS升级，定位顺序和规则可能改变。
```
-------
#### GPX FILES

[GPX](https://zh.wikipedia.org/wiki/GPX)（**GPS eXchange Format**，GPS交换格式）是一个XML格式，为应用软件设计的通用[GPS](https://zh.wikipedia.org/wiki/GPS)数据格式。它可以用来描述路点、轨迹、路程。它用来保存位置，海拔和时间。

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1126909-2b527f29d77c0ac8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Xcode也是用GPX 文件来存储位置信息.所以，我们还可以自定义一个GPX文件，模拟任意的位置信息。

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1126909-8fc251d290296250.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

https://blackpixel.com/writing/2013/05/simulating-locations-with-xcode.html
https://github.com/whilethis/location-simulation

------

大多数App取定位信息的时候,都是调用用系统的[Core Location API](http://nshipster.cn/core-location-in-ios-8/) ,[CLLocationManager](
https://developer.apple.com/library/ios/documentation/CoreLocation/Reference/CLLocationManager_Class/index.html).

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/1126909-9dbcead93513214b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然而在此所有app调用定位服务后，获取到的都是我在FakeGPS工程中选的Default Location的定位信息。Why ? 各种联想中...


