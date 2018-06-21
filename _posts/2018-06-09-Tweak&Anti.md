---
title: Tweak&Anti(一)

layout: post

tags: 逆向

---
## Tweak项目创建
* 安装THEOS
* 在.bash_profile中配置环境变量
* 使用nic.pl来生成模板
* 根据不用需求来创建项目(11 是tweak)


## Anti
* 首先我们先创建一个空白项目,添加一个touchBegan方法,不出意外,他点击屏幕就是崩溃的
![](https://ws2.sinaimg.cn/large/006tNc79ly1fs4wj45djfj31c60qs42x.jpg)

* 接下来我们就创建一个Tweak工程来hook我们的代码😆
![](https://ws4.sinaimg.cn/large/006tNc79ly1fs4wmem51bj31780ckt9r.jpg)

* 使用命令讲我们写好的代码,安装到我们的手机上,安装完毕后会刷新SpringBoard

	```c
	$ make package; make install

	```

* 当我们再次跑起来我们的项目点击屏幕的时候就不会再闪退啦😆,显示的就是我们在tweak中编写的"和平相处"
![](https://ws1.sinaimg.cn/large/006tNc79ly1fs4wq6qjvxj31c6168453.jpg)

> 动态库就插入成功咯,接下来我们再试试如何防护如何防止我们的项目被注入代码,这就要了解注入的原理,我们只有知道了他是如何注入的,我们才能想到对应的办法不是?理论上说逆向和反逆向是没有绝对安全的,只是正向开发者和逆向开发者对着烧脑,看谁的脑洞更大一些,谁就会更胜一筹😆

我们知道,app的启动,以及各种动态库的加载,都是依靠dyld(dynamic loader)完成,使用DYLD_INSERT_LIBRARY参数进行注入,那也就是说,如果我们不允许它插入库,是不是就能抵御住注入呢?通过百度之后得知,在Build Settings中找到“Other Linker Flags”
在其中加上

```c
-Wl,-sectcreate,__RESTRICT,__restrict,/dev/null
```
![](https://ws4.sinaimg.cn/large/006tNc79ly1fs4xbn83xsj31bs0ywacz.jpg)

此时,我们再次将项目跑起来,就不再允许动态库注入啦

> 此时我们就完成了防御工作,那我们再想调试肿么办呢?由于[dyld苹果已经给予开源啦](https://opensource.apple.com/source/dyld/dyld-210.2.3/src/dyld.cpp),从源码上可以看到

```cpp
static bool hasRestrictedSegment(const macho_header* mh)
{
	const uint32_t cmd_count = mh->ncmds;
	const struct load_command* const cmds = (struct load_command*)(((char*)mh)+sizeof(macho_header));
	const struct load_command* cmd = cmds;
	for (uint32_t i = 0; i < cmd_count; ++i) {
		switch (cmd->cmd) {
			case LC_SEGMENT_COMMAND:
			{
				const struct macho_segment_command* seg = (struct macho_segment_command*)cmd;
				
				//dyld::log("seg name: %s\n", seg->segname);
				if (strcmp(seg->segname, "__RESTRICT") == 0) {
					const struct macho_section* const sectionsStart = (struct macho_section*)((char*)seg + sizeof(struct macho_segment_command));
					const struct macho_section* const sectionsEnd = &sectionsStart[seg->nsects];
					for (const struct macho_section* sect=sectionsStart; sect < sectionsEnd; ++sect) {
						if (strcmp(sect->sectname, "__restrict") == 0) 
							return true;
					}
				}
			}
			break;
		}
		cmd = (const struct load_command*)(((char*)cmd)+cmd->cmdsize);
	}
		
	return false;
}

```
![](https://ws4.sinaimg.cn/large/006tKfTcly1fs5c7def8dj312l0bpgnx.jpg)

> 我们只要把这个segment字段篡改,就能使条件不满足,那我们就能继续Tweak调试啦,所以我们接下来尝试修改一下[SynalyzeIt!Pro118](https://waitsun.ctfile.com/fs/CUV156981940),发现可以做到修改segment字段,重新跑起来项目,又可以hook啦
