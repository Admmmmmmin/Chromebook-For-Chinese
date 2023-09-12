# 在中国如何使用Chromebook

## 激活服务

Chromebook 到手以后，使用 ChromeOS 的时候需要激活，登录好谷歌帐号，以后就可以直接登录了。

~~激活的时候正常需要翻墙，可以使用fqrouter2等进行手机USB分享，不过fqrouter是root后的android设备使用起来更佳。~~ fqrouter2 早已无法使用，但是可以在局域网中使用代理服务后，将 Chromebook 连接同一个局域网，设置代理激活。

## 替换原厂固件和操作系统

适用于：

1. 具备一定的 GNU/Linux 知识，希望在 Chromebook 上获得完整的 GNU/Linux 作业环境的用户;

2. 因为种种原因希望替换 Google 提供的网络服务的用户。（也可以试试 FydeOS 的）

注意：替换 BIOS 来安装新操作系统的方法不是太保险。

请点击 [为 Chromebook 刷写第三方操作系统](linux-on-seabios-and-coreboot.md) 。

## 在 Chromebook 上安装 Linux （以 Kali 为例）

### 安装方法

#### [Crouton](https://github.com/dnschneid/crouton)

**截至20230903，因相关文件缺失，Crouton 构建仍处于不可用状态（报错`sed: can't read common/cras_messages.h: no such file or dictionary`），这主要还是音频导致的问题，具体可以到 Crouton 原仓库查看相关 issue。**

将系统版本退回至110及之前版本据称可以恢复 Crouton 脚本的可用性，未验证，但参见[此 issue](https://github.com/dnschneid/crouton/issues/4945)。

可以通过在命令前追加不使用音频的修正，例如：`sudo CROUTON_BRANCH=silence crouton -r kali-rolling -t core,extension` 来正常安装。安装完成之后不会识别本机的音频硬件，但可以识别外接设备，参见[此讨论](https://github.com/dnschneid/crouton/discussions/4969)。

---

下载官方 Crouton :

```bash
wget https://goo.gl/fd3zc
```
某些 shell 不会预装 wget，此时可以使用 curl：
```bash
curl -O https://goo.gl/fd3zc
```

[Crouton 安装需要的声卡驱动](https://raw.githubusercontent.com/Admmmmmmin/Chromebook-For-Chinese/master/third-party/audio/latest.tar.gz)

##### 修改声卡驱动的两种方法：

1.1 下载[Crouton](https://github.com/dnschneid/crouton)，打包下载，**Download Zip**

1.2 更改**targets/audio**文件:

第47行：

```sh
( wget -O "$archive" "$urlbase/$ADHD_HEAD.tar.gz" 2>&1 \
                                    || echo "Error fetching CRAS" ) | tee "$log"
```

改为：

```sh
( wget -O "$archive" "https://raw.githubusercontent.com/Admmmmmmin/Chromebook-For-Chinese/master/third-party/audio/latest.tar.gz" 2>&1 \
                                    || echo "Error fetching CRAS" ) | tee "$log"
```

1.3 直接运行 `installer/main.sh` ,或者 make 自己的 Crouton。

**或者**

2.1 直接按原仓库安装方式下载 Crouton，亦即：
  > 下载 https://goo.gl/fd3zc 处的文件后，运行以下命令：
  > ```
  > sudo install -Dt /usr/local/bin -m 755 ~/Downloads/crouton
  > sudo crouton
  > ```
  > 此时应该可以看到相应的提示信息。

2.2 将 [audio 文件](https://github.com/dnschneid/crouton/raw/master/targets/audio)保存为无后缀名的 audio 于 `/tmp/`内的任意文件夹中，以建立 audi 文件夹为例：
```bash
sudo mkdir /tmp/audi && sudo curl -# -o /tmp/audi/audio https://raw.githubusercontent.com/dnschneid/crouton/master/targets/audio
```

2.3 修改之后利用 -T 参数指定 crouton 使用的 target 文件：
```bash
sudo crouton -n kali -r kali-rolling -T /tmp/audi/audio
```
安装完成之后 audi 文件夹可以删除。

#### Chrubuntu

[参见此文章](http://chromeos-cr48.blogspot.com/2013/10/chrubuntu-for-new-Chromebooks-now-with.html) 或其 [互联网存档](https://web.archive.org/web/20230821052218/http://chromeos-cr48.blogspot.com/2013/10/chrubuntu-for-new-Chromebooks-now-with.html)

简单记录如下：

**注意**：以下操作将抹除 ChromeOS 的用户数据，请做好重要数据的备份

1. 进入开发者模式
2. 不要登录，确保联网，然后按下 <kbd>CTRL</kbd> + <kbd>ALT</kbd> + <kbd>=>(F2)</kbd> 进入 TTY
3. 登录，用户名 chronos ，密码不填
4. 输入命令：

```bash
curl -L -O http://t.cn/RA3xv2I; 
sudo bash RA3xv2I
```

5. 根据提示选择分区大小，将硬盘重新分区，之后会自动重启一次
6. 重新分区后，需要再执行一次上面的命令。注意默认会安装最新版的 Ubuntu，如需安装 LTS 版本请在后一句最后加上 ` -u lts`，更多选项请加上 ` -h` 查看
7. 接下来根据提示进行即可，安装完后默认的主机名为 chrubuntu，用户名跟密码都为 user

##### 修改说明

- 文件 chrubuntu.sh 来自于 http://goo.gl/9sgchs ，作了如下改动：
  1. 修改官方源为国内源，将 cdimage 的源改成 USTC，更新源换成 THU
  2. 去掉了安装 Google Chrome 的部分
  3. 最新15.04版本内核为3.19，应该不需要替换内核补丁。最后是用U盘装好的。
  4. [Desktop install](https://github.com/karlssonjohan/ubuntu-on-Chromebook )
- 文件 cros-haswell-modules.sh 来自于 [短链接goo.gl](http://goo.gl/kz917j) ，未作改动

U盘引导安装：

用到的工具：Win32DiskImager

[关于使用后的U盘恢复](http://blog.csdn.net/u011538446/article/details/11590825)

#### elementary OS

[elementary OS installation script for Chromebooks](https://github.com/Setsuna666/elementaryos-Chromebook )

## 运行安卓程序

## 触摸板内核问题
如果是刚刚装好系统，还需要先安装 sudo gcc 等软件包。

```bash
$sudo pacman -S wget sudo patch make gcc
wget http://t.cn/RA3CM2t
bash RA3CM2t \$mykern
rm RA3CM2t
```

## 代理服务

+ shadowsocks-chromeapp: [~~Chromebook /ChromeOS安装Shadowsocks扩展教程~~](https://www.dogfight360.com/blog/?p=250) 原文已失效，请参见[互联网存档](http://web.archive.org/web/20170221235509/https://www.dogfight360.com/blog/?p=250)。
+ ~~fqrouter2~~
+ VPN
+ SSH代理