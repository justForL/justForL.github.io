---
title: React-Native学习总结

layout: post

tags: RN

---

## 前言

最近因为跳槽换工作的原因一直也没时间来得及做笔记,因为新东家用到的就是RN,一直以来都因为惰性,学习医院不是特别强烈,导致反反复复的进度特别慢,这下好了逃不过了,出来混迟早是要还的....

由于需要快速上手,所以也没有来得及系统的学习js以及[ES6标准](http://es6.ruanyifeng.com/),所以在初期学习时候凭借浅薄的js知识直接学习RN,浪费了很多时间去理解标准写法和记忆各种语法😢,书到用时方恨少啊....

## 学习路线图
![](https://ws3.sinaimg.cn/large/006tNc79ly1ft3jxciut7j31680zqq8w.jpg)

## UI部分
### 页面布局
> 在原生中,我们如果要写界面最快的方式就是拿xib或者sb直接妥妥拽拽就ok了,如果是纯代码就用Masonry或者手算frame的方式来完成页面布局,而在RN里的页面布局是采用类似css的方式来写页面,这让我一个习惯了原生开发的iOSER,着实闹心了一会,因为我要从头开始捡起来css

#### Flex
在Flex布局中,我们可以指定width和height,不过一般都是按照百分比的形式来制定宽高,因为要同时兼顾ios&android,这就和我们原先不一样了,只考虑ios一方就不行了😭
![](https://ws4.sinaimg.cn/large/006tNc79ly1ft3ks1gpqzj30lm0bedhb.jpg)

在布局方面,比较需要注意的就是**justify-content**和**align-items**属性,这个是根据主轴和副轴来设置布局的,很有用!

微博上收集到的[Flex布局口诀](https://weibo.com/1712131295/CoRnElNkZ?ref=collection&type=comment#_rnd1531116658286)

### 控件使用
> 在iOS原生项目中,每天接触最多的就应该是UITableView了,但凡是滚动视图,我们一般都是依靠TableView来实现的,学习RN的时候,我也是以这个组件为切入点,开始了我的学习之路😆


#### ListView
在ListView的使用中,也同样需要制定数据源,不过和我们原生不同的地方是,渲染机制不同,使用方法大体一致.在ios原生里,我们只需要关心数据源就好啦,操作数据源数组,数据更新时,直接reloadData即可,我也是用这样的方式来尝试用于ListView,奈何头文件中没找到reload方法并且第一次跳进头文件后也是一脸懵逼,不得不开始百度.
> 原来在RN中建立数据源的时候,需要制定它的渲染策略,然后更新数据的时候也是根据ListView的dataSource提供的clone方式来更新数据源,并不可以直接修改dataSource

![](https://ws4.sinaimg.cn/large/006tNc79ly1ft3lbzbxxaj30rc0c40uk.jpg)
更新数据源的时候,也是根据我们提前制定好的渲染策略来实施更新操作
![](https://ws4.sinaimg.cn/large/006tNc79ly1ft3lcmfy17j312u044q3l.jpg)
#### RefreshControl
这个刷新组件,并不像我们在原生中使用MJRefresh一样,只需要给listView指定refreshing属性即可,这里是直接使用的,在项目里我们可以对Refresh进行封装,全局公用一个,一处修改处处使用..
![](https://ws2.sinaimg.cn/large/006tNc79ly1ft3lfq3tf3j30v80bcac2.jpg)

上拉加载更多也和我们在原生中不一样,只需要指定listview滚到到底部后,开始刷新数据就行了,使用起来真的超方便

```
onEndReached={ this._toEnd.bind(this) }
onEndReachedThreshold={10}
scrollEventThrottle={50}

```
#### TouchableOpacity

这个组件,使用起来真的没什么特殊的,就是在想要有响应事件的试图外面包装一层TouchableOpacity即可,指定onPress方法,就万事大吉了😆
[官方使用文档](https://reactnative.cn/docs/0.51/touchableopacity.html#content)
#### Dimensions
是用来获取屏幕的尺寸滴,一般常用操作就是获取屏幕宽高

```
width:Dimensions.get('window').width,
height:Dimensions.get('window').height,

```
也可以

```
const {width,height} = Dimensions.get('window')
```
#### Image
就是简单的标签,用就完了😆
#### Text
同上
#### NavigationTabBar
TabBar使用上比较通俗易懂😆直接粘代码了

```

import LJHome from '../tabs/main/LJHome'
import { createBottomTabNavigator } from "react-navigation";
import TabBarIcon from '../components/TabBarIcon'
//普通tab页面路由
const BottomTabRouters = createBottomTabNavigator(
    {

        Home: {
            screen: LJHome,
            navigationOptions: {
                title: "首页",
                tabBarIcon: ({ focused, tintColor }) => TabBarIcon(focused, "home")
            }

        }
        ,
        Shop: {
            screen: LJHome,
            navigationOptions: {
                title: "赚多多",
                tabBarIcon: ({ focused, tintColor }) => TabBarIcon(focused, "home")
            }
        },
    },
    {
        initialRouteName: "Home",
        backBehavior: "none",
        tabBarPosition: "bottom",
        tabBarOptions: {
            activeTintColor: '#F87D12',
            inactiveTintColor: '#8F8F8F',
            style: {
                width: "100%",
                justifyContent: "center",
                height: 50,
                backgroundColor: "#FFF",
            },
            labelStyle: {
                marginTop: -4,
            },
            tabStyle: {
                backgroundColor: '#FFF',
                paddingBottom: 6,
            }
        },
    }
);


export default BottomTabRouters;

```

## 网络
## Native交互

在ios项目里面新建一个测试工具类,需要做的就是一些固定套路,引入头文件,以及遵守协议

```objective-c
#import <UIKit/UIKit.h>
#if __has_include(<React/RCTBridgeModule.h>)
#import <React/RCTBridgeModule.h>
#else
#import "RCTBridgeModule.h"
#endif
@interface TestTool : NSObject<RCTBridgeModule>

@end

```

![](https://ws2.sinaimg.cn/large/006tNc79ly1ft3m2o9ur2j31a405aq4e.jpg)

在OC里面怎么实现方法,如何判断进行回调就涉及到业务层面啦,就不一一赘述啦,接下来是我们回到rn里如何去调用

```

NativeModules.TestTool.testFuc('123').then((result) => {
            
        }).catch((err) => {
            
        });
        
```
## 存储
## 状态管理
### props&state
## 其他资源


