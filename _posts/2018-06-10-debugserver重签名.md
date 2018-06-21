---
title: debugserver签名问题

layout: post

tags: 逆向

---

本身iphone本身是不具备debugserver的,但是只要经过xcode调试过app后,就会由xcode安装到手机上一个debugserver,目录就在`/Develop/usr/bin/`目录下,但是这个debugserver缺少我们需要的权限,因此我们需要手动对其进行赋予权限以及重签名,在百度上一顿操作后,看到[狗神的文章](http://iosre.com/t/debugserver-lldb-gdb/65),因此做一下记录