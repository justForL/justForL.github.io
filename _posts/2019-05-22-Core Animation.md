---
layout: post

title: 动画学习

tag: iOS动画

---

> 由于上一次再酷我面试,死于动画一直耿耿于怀,被一些特别基础的动画效果拦于门外,心有不甘,趁着这次重新找工作在家,要把动画捡起来,巩固一下自己的动画知识!!!

下图描述了CoreAnimation与UIKit框架的关系:

![](http://ww2.sinaimg.cn/large/006tNc79ly1g39v4msz34j313e0u0agw.jpg)

## Core Animation

Core Animation的关系结构图如下:

![](http://ww2.sinaimg.cn/large/006tNc79ly1g39vaoyuvcj313s0iawjq.jpg)

CAAnimation是核心动画的基类，不能直接使用，主要负责动画的时间、速度等，本身实现了CAMediaTiming协议。

![](http://ww1.sinaimg.cn/large/006tNc79ly1g39vch4h51j30u00vpqgm.jpg)

### CAMediaTiming协议 
像duration，beginTime、repeatCount、speed、timeOffset、repeatDuration、autoreverses这些时间相关的属性都在这个类中。协议中的这些属性通过一些方式结合在一起，准确的控制着时间。

### CAMediaTimingFillMode介绍

```
kCAFillModeRemoved：这个是默认值，也就是说当动画开始前和动画结束后，动画对layer都没有影响，动画结束后，layer会恢复到之前的状态
kCAFillModeForwards：当动画结束后，layer会一直保持着toValue的状态
kCAFillModeBackwards：如果要让动画在开始之前（延迟的这段时间内）显示fromValue的状态
kCAFillModeBoth：这个其实就是上面两个的合成.动画加入后开始之前，layer便处于动画初始状态，动画结束后layer保持动画最后的状态

注意必须配合animation.removeOnCompletion = NO才能达到以上效果
```
## 重识CALayer
第一次意识到CALayer这个东西的时候是很很久以前的一次面试过程中,view的效果是如何显示出来的,我们开发的时候其实并没有意识到view和layer真正的关系,而且我们在操作view的frame的时候也没有刻意的去操作layer,其实这都是苹果为我们做好的,view和layer的关系其实就是,view只负责处理事件是layer的容器,真正负责界面效果展示的其实就是layer.

`Ps:`继承自CAAnimation，不能直接使用，要想创建动画对象，应该使用它的两个子类：CABasicAnimation和CAKeyframeAnimation

## Layer和动画的关系

在使用CoreAnimation的动画的时候，是把创建的动画放到layer上，而简单的使用动画，很多时候是使用[UIView animation...]，那么后者其实本质是内部建了一个动画放到了layer上吗？是的，动画的载体就是layer，这就是它们的基本关系。但为了更高效的动画，还有更多的细节。
如果你做过位移的动画，并且试着在动画的过程里去输出view的位置，你会惊讶的发现：在动画开始后，view的frame就已经是结束位置的值了！
按照常识理解，view的位置应该是随着时间不断变化的，而这个理解上的错差正是理解动画内核的一个好的窗口。
从上面的现象至少可以得出一点：就是你眼睛看到的，跟系统里的数据不是一致的，动画可能是一个欺骗把戏。

## Layer的层次结构
> Layer的层级结构 Layer tree 分为三种:

1. Model Tree: 我们常说的layer;
2. Presentation Tree: 呈现出来的Layer,也就是我们做动画时看到的那个,可以通过layer.presentationLayer来获得
3. Render Tree: 私有,无法访问.主要是对Presentation Tree数据进行渲染,并且不会阻塞线程
