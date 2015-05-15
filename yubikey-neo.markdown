---
title: Yubikey NEO入手小计
date: 2015-05-15
---

3月下旬去了趟美国, 顺便买了两个Yubikey Neo回来, 这两天开始折腾了一下, 到能够使用的程度,
折腾过程中遇到不少坑, 本文以不求甚解的态度把这些坑都一一记录下来, 希望方便自己和其他想要折腾的同学.

# 购买

其实第一个比较核心的问题是为什么要买以及怎么买.

简单说下Yubikey和yubico公司, Yubikey是一个电子令牌的系列, 这个系列特点的是便携并且价格低廉.
所谓电子令牌, 我的理解就是实现了某些加密算法或者认证协议的芯片, 通过外部接口(一般是USB接口)来提供加密或认证服务.
电子令牌的好处是将密钥和认证信息保存在便于携带的令牌上, 降低了在存储和传输过程中, 密钥或者认证信息被盗的风险.

我买Yubikey的主要原因是看中了它的OpenPGP Card的功能, 这样我就可以把PGP的私钥以及加密/签名的功能都放在电子令牌上,
这样就不同担心密钥被盗或者被误删了.

Yubikey系列目前有[五个产品][Yubikey], 其中我买的是Yubikey NEO, 对于其他版本的限制和功能我也不是特别清楚,
有兴趣的同学可以自行研究下. Yubikey NEO在[亚马逊]和万能的淘宝上也能够买到,
但是买的事情要看清楚固件版本, 有些固件版本是不支持U2F的, 而Yubikey又因为安全性的考虑[不支持固件升级].

# 折腾!

所有的折腾过程都在ArchLinux下进行, 如果你使用的是其他Linux发行版, 我想过程应该大同小异.
而如果你希望能在Mac或Windows上使用Yubikey NEO, 坑的数量可能更多或者更少, 但是还是可以从本文获得一些借鉴.

需要说明的是, 这并不是一篇Yubikey NEO使用方法的教程(因为教程写起来太麻烦了, 并且有很多好心人已经[写过][教程]).
你可以在使用教程之前, 阅读下本文, 对Yubikey NEO的功能有个大致的印象, 避免发生悲剧, 或者像我一样, 折腾了两天才搞定.

## 驱动和工具

其实Yubikey是不需要的驱动的, 因为如果要做OTP或这U2F的话, Yubikey本身会作为一个HID设备, 就是一个USB输入设备,
而在Linux内核中, USB HID的设备是已经写好的了, 这就是为什么你把鼠标和键盘插上之后, 就可以直接工作的原因.
而如果是作为OpenPGP Card使用的话, Yubikey Neo已经遵照相应的协议提供了供gpg使用的接口, 因此也不需要安装驱动.

但是出于两个其他的原因, 我们需要安装一些工具软件. 原因之一是我们需要对Yubikey进行一些配置, 包括工作模式等;
原因之二是为了能够让终端用户访问Yubikey设备.

### [yubikey-personalization][ykpers]

上面所说的两个目的都可以通过安装[yubikey-personalization][ykpers]来实现, 因为[yubikey-personalization][ykpers]已经自带了
一个udev的rule, 能够给Yubikey设备配置正确的环境变量, 从而保证能够被终端用户使用.

虽然AUR上有yubikey-personalization的包, 叫做ykpers, 但是我并不推荐安装它, 因为AUR上的ykpers的版本有些老, 不能支持新固件版本的
Yubikey了. 可以在[yubikey-personalization][ykpers]的Compatibility页看到每个版本对应的支持的固件版本.

如果你跟我一样使用的是固件版本为3.3的yubikey, 那么你可以用在archlinuxcn源里已经打包好的ykpers, 也就是我正在使用的,
接下来本文会假设你使用的是固件版本3.3的yubikey并且使用archlinuxcn源里的ykpers.

在安装好ykpers之后, 首先你需要给Yubikey设置工作模式, 讲Yubikey插入电脑之后, 运行

`ykinfo -v`

可以参看是否能够识别当前插入的Yubikey, 能够识别的话就是可以进行后续的操作了.
通过运行

`ykpersonalize -m <mode>`

可以设置Yubikey的工作模式, 具体每个数字代表怎么的工作模式可以通过`man ykpersonalize`来查看.
但这里有一个可能的坑是, 先不要选择一个不带OTP功能的模式, 因为如果如果选择这样的模式, 比如说85,
那么ykpers可能就没办法识别出Yubikey来, 而造成不能把Yubikey设置为其他模式, 万一<del>手滑</del>设置成了这样的mode,
那么也不用慌张, 可以通过安装另一个管理软件ykneomgr来重新设置, 不过ykneomgr的依赖很多, 也会用到智能卡方便的daemon,
配置起来极其繁琐, 因而总的原则是不推荐..

将mode设置为85还有另一个潜在的问题, 那就是ykpers自带的udev rules里没有针对85 mode的设置, 因此会导致插上Yubikey之后,
gpg没办法访问. 所以如果希望在85 mode下继续使用Yubikey, 就必须修改udev rules, 在`ATTRS{idProduct}`的取值范围中, 加入`0115`

不过, 话说回来, 我为什么要<del>吃饱了没事做</del>把模式设为85呢, 我相信你仔细看mode就会发现85的mode下禁用了OTP,
而我确实并不需要OTP功能, 如果你希望使用OTP功能, 那么大可不像我这么麻烦.

## 使用

折腾了一大圈, 好不容易把Yubikey识别出来, 并且设置了期望的工作模式, 那么就下一步就应该是真正开始使用了.
首先正如大多数教程里面说的那样, 我们应该先运行`gpg --card-status`来检查gpg是否能够访问Yubikey, 如果不能, 可以重启gpg-agent重试.

一个Yubikey Neo中只能存放三个2048的PGP私钥, 而且为每个私钥分配了不同的功能, 分别是签名/解密/认证.
那么接下来你就可以<del>欢快地</del>按照任何一个教程所述来将密钥"存放"在Yubikey上了, 你可以选择直接在Yubikey上生成, 或是把已经生成好的key移动到Yubikey上.
但无论怎样, 当你把私钥放到Yubikey上时, 你本地密钥环中的私钥就不复存在了, 所以如果你希望能够backup私钥, 那么合理的做法是先生成私钥,
然后再将私钥到处, 最后再把私钥移动到Yubikey上.

到此基本上大功告成了, 等等你说gpg不能生成认证密钥(Authentication key)? 其实我个人也遇到这个问题,
而且作为一个gpg的老玩家, 我记得多年之前, gpg在生成密钥的时候还是可以选择密钥类型是否为A类型, 也就是Authentication key的.
但是我后来使用gpg 2.1的时候无论如何也没办法生成认证subkey, 直到后来, 我发现真相是gpg 2.1把Authentication key<del>作为隐藏boss</del>
而隐藏了起来, 需要在gpg命令行加入--expert选项, 进入"专家模式"才能设置密钥类型为Authentication, 比如:

`gpg --expert --edit-key`

那么认证密钥究竟有什么用呢? 我也说不清楚, 我个人是把它当作ssh的私钥来用的, 这样我正好可以把ssh私钥的拷贝也从电脑上删除了.
至于怎么用Yubikey上的私钥来作为ssh的私钥, 其实很简单, 因为gpg-agent本身也可以做为ssh-agent, 并且只需要两步就能完成:
1. 开启gpg-agent的--enable-ssh-support选项
2. 把私钥的*keygrip*放到.gnupg/sshcontrol文件里

(keygrip的解释:That is a protocol neutral way to identify a public key. It is a hash over the actual public key parameters. 可以通过命令`gpg -k --with-keygrip`得到)

## Tips

1. Yubikey有一个PIN和admin PIN
2. PIN用来控制密钥的使用, 默认是加密和认证之前必须输入过PIN码(一次即可), 而每次签名的时候, 都需要输入PIN码
3. admin PIN用来管理密钥, 如重设密钥
4. PIN值连续三次错误Yubikey即被锁定(block), 接下来使用的时候仍会要求输入PIN码, 但无论PIN码是否正确, 都会返回Card error.
5. 可以用admin PIN重置或unlock Yubikey
6. admin PIN密码三次输入错误, 即被锁定, 不能进行任何Yubikey管理工作(如增删,修改密钥), 但仍可以在丢失密钥的条件下恢复使用, 即[Reset][Reset]
7. 以上加起来的意思就是, 各种PIN码尽量不要出错, 特别是admin PIN, 当然不用担心, 只要你的私钥备份还在, Yubikey无论怎样都可以使用

[Yubikey]: https://www.yubico.com/products/yubikey-hardware/
[亚马逊]: http://www.amazon.com/Yubico-Y-072-YubiKey-NEO/dp/B00LX8KZZ8
[不支持固件升级]: https://www.yubico.com/faq/upgrade-yubikey-firmware/ 
[教程]: https://www.esev.com/blog/post/2015-01-pgp-ssh-key-on-yubikey-neo/
[ykpers]: https://developers.yubico.com/yubikey-personalization/
[Reset]: https://developers.yubico.com/ykneo-openpgp/ResetApplet.html
