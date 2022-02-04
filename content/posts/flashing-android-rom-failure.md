---
title: "记录一次失败的刷机经历"
date: 2021-12-22T01:46:54+08:00
tags:
  - 安卓
categories:
  - 安卓
---

好吧，之前给小米 8 刷了 lineageos，讲道理还是很好用的。miui 优化太差，换成 lineageos 以后才发现原来系统不掉帧居然是如此的流畅。不过在折腾的时候感觉手机还是 root 一下比较好，于是我就准备往手机里刷一个 magisk。当然为了保险起见我先练习了一下，在退役许久的小米 5 上先刷了一次试了一下，结果很成功。

## 刷入 magisk

然后我就往小米 8 上刷 magisk，具体操作到也不算是很麻烦，因为这几个手机都支持 ramdisk，所以只需要在手机上先下载 magisk app，然后把刷机包 zip 里面的 boot.img 解压出来，放到手机里面。

```sh
adb push boot.img /sdcard
```

然后在 magisk app 里面选择修补文件，这样就会得到一个修补过后的`magisk-boot.img`，然后拉出来到电脑端。

```sh
adb pull /sdcard/Download/magisk-xxx.img
```

最后再把手机重启到工程模式，将镜像刷入到 boot 分区即可。

```sh
adb reboot bootloader
fastboot flash boot magisk-xxx.img
```

当然如果手机支持 ramdisk 的话，还有另外一种刷机方法，那就是直接把下载的`magisk.apk`改名成`magisk.zip`，在 twrp 里面刷进去，不过这种方式虽然看起来巧妙，但是已经被官方定为不推荐的方法，原因是 magisk 在 recovery 模式下有一些手机信息获取不到，所以最好还是通过上面的 magisk app 的方式刷入。

## 问题出现

当然，理论上是这样的。但是当我的小米 8 刷入以后却发现开不了机了。一番研究下来发现应该是锁屏密码的问题，这也是安卓手机的一个安全特性，一旦你用锁屏密码保护手机，那么其实数据分区就会自动加锁。但是假如要刷机的话这就尴尬了，当然这其实也并不是什么问题，毕竟 twrp 也有解锁功能。如果你没办法使用屏幕上的解锁功能的话，那 twrp 其实还有命令行可以使用<https://twrp.me/faq/openrecoveryscript.html>。

当然，这个时候其实也没什么问题，毕竟手机在那里，recovery 也在那里，数据还在那里。所以我又试图将原版的`boot.img`刷回去看看行不行，然后再次失败。这么看来估计除非进系统再取消加密，否则这个系统肯定是进不去了。所以为了保险起见，我用 twrp 将系统分区和数据分区备份了一遍。

## 问题加重

备份完手机，我想着用 adb 把备份拉出来，顺便把以前存的一点图片也拉出来。我先试图拉图片，然后因为有个图片文件夹有中文名称，所以出现编码错误，没拉成功。这时候我犯了第二个蠢，那就是想着既然备份了那就应该没事，所以没有把备份拉出来。

## 问题再加重

然后我做的事情就是试图重刷系统，然后在想办法回去。这时候我又犯了第三个蠢，那就是手贱在 twrp 把系统分区和除了内置存储以外的分区格式化了。其实刷机的时候，并不需要格式化系统分区，因为刷机包会自动处理这件事情。但是我可能就是有一点强迫症，刷机时候喜欢没事点点。但是这时候好巧不巧就出问题了，twrp 不知道咋回事进不去了，这就尴尬了。进不去 twrp，就没办法解锁内置存储，也就没办法拉去备份，这下彻底尬住了。最尴尬的是，我刷第三方 ROM 自带的 recovery 可以启动，偏偏 twrp 就不行，真的是蚌埠住了。

## 问题彻底解决（全搞砸了也算解决不是吗）

好吧，虽然事情也没怎么样，只是 recovery 坏了而已，不过我想了想，也没什么好的解决办法，要么试试先线刷回官方系统，然后看看能不能解锁分区，先把我的备份搞出来。这时候我反了第四个蠢，那就是没注意线刷的选项，而默认选项连内置存储也会覆盖。等到我线刷完了我才发现这个选项，这时候已经晚了。等我进去系统一看，哦嚯，内置存储空空如也。

好嘛，这下也不用想着怎么恢复了，反正都没了，就在重刷一次机吧。当然，讲道理我手机上也没有什么重要的东西，什么聊天记录什么短信统统都没有，不过之前还是稍微保存了一点自己感觉有意思的图片，之前刷机的时候还特意看了一下保留出来，结果这下也不需要了。

其实讲道理 lineageos 真的挺好用的，维护的也不错，当做主力系统用没什么问题。不过既然再次重新归零，我这次就找了另外一个刷机包 pixelexperience，小米 8 正好有一个安卓 12 的 beta 包，我就刷进去玩玩，结果以外的感觉还不错，于是干脆当做一个主力系统试着用用吧。