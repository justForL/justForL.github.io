---

title: IJKMediaFramework学习

layout: post

---

## 前言

>看到了最右的招聘简章,需要有想要视频的相关经验,故研究一下,目前github上比较火的就是IJKMediaFramework框架,是由b站开源的视频播放框架,因此以此作为切入点.

首先需要到ijkMediaFramework主页下clone项目

[ijk主页](https://github.com/Bilibili/ijkplayer)

![](https://ws2.sinaimg.cn/large/006tNc79ly1fpm1yyqghcj30630e2t9o.jpg)
<center>下载后并解压项目目录</center>
## 编译 ijkplayer

* 打开终端, cd 到jkplayer-master文件夹中
![](https://ws3.sinaimg.cn/large/006tNc79gy1fpm20jtiowj30by0frgn1.jpg)

* 执行命令行`./init-ios.sh` 实质是下载ffmpeg,需等待30分钟左右,依据个人网络状况
![](https://ws2.sinaimg.cn/large/006tNc79ly1fpm228dvz9j30h704jdgd.jpg)

* cd到ios文件夹下,在终端依次执行,编译ffmpeg

```
./compile-ffmpeg.sh clean

./compile-ffmpeg.sh all
```
## 打包成framkework库

* 打开工程

![](https://ws1.sinaimg.cn/large/006tNc79ly1fpm2518joxj30jg0fnmyy.jpg)

![](https://ws1.sinaimg.cn/large/006tNc79ly1fpm25mkozbj30jb096dh5.jpg)

* 选择edit scheme,并在Run选项卡下,讲Build configuration下选择Realese


![](https://ws4.sinaimg.cn/large/006tNc79ly1fpm27e4vxij30jg0avdgo.jpg)

* 分别编译真机和模拟器后进入finder可以查看到对应的编译结果

![](https://ws4.sinaimg.cn/large/006tNc79ly1fpm290jc0uj30h50aat9f.jpg)

`注意` 编译的文件是包内的framework
![](https://ws4.sinaimg.cn/large/006tNc79ly1fpm29w6086j30ij0crq3p.jpg)

* 打开终端, 进行合并, 命令行具体格式为:

```
lipo -create "真机版本路径" "模拟器版本路径" -output "合并后的文件路径"

```
![](https://ws3.sinaimg.cn/large/006tNc79ly1fpm2ar3ff5j30hd0apdgk.jpg)

* 下面很重要, 需要用合并后的IJKMediaFramework把原来的IJKMediaFramework替换掉, 如下图

![](https://ws4.sinaimg.cn/large/006tNc79ly1fpm2b7ps7kj30ha0az0tv.jpg)

## iOS工程中集成ijkplayer

* 讲framkework文件拖入项目中
* 导入头文件后编译,不出错即打包成功
* 参考官方demo编写测试界面
* 
![](https://ws2.sinaimg.cn/large/006tNc79gy1fpm2dvt2ztj30gk0vmjy6.jpg)