
# Termux:X11

[![Nightly build](https://github.com/termux/termux-x11/actions/workflows/debug_build.yml/badge.svg?branch=master)](https://github.com/termux/termux-x11/actions/workflows/debug_build.yml) [![Join the chat at https://gitter.im/termux/termux](https://badges.gitter.im/termux/termux.svg)](https://gitter.im/termux/termux) [![Join the Termux discord server](https://img.shields.io/discord/641256914684084234?label=&logo=discord&logoColor=ffffff&color=5865F2)](https://discord.gg/HXpF69X)

一个[Termux](https://termux.com) 的X11服务器插件应用

## 关于
Termux:X11是一个功能完备的X服务器。它使用Android NDK构建，并经过优化，以便与Termux配合使用。

## 子模块注意事项
这个仓库使用了子模块。请使用

```
~ $ git clone --recurse-submodules https://github.com/termux/termux-x11 
```
或者
```
~ $ git clone https://github.com/termux/termux-x11
~ $ cd termux-x11
~ $ git submodule update --init --recursive
```

## 它的工作原理是什么？
就像任何其他X服务器一样。

## 安装指南
该应用程序仅适用于Android 8及更高版本的设备。为此，您必须启用`x11-repo`仓库，可以通过执行`pkg install x11-repo`命令来完成

为了使X应用程序正常工作，您必须安装Termux-x11的配套包。您可以通过从[最后一个成功构建](https://github.com/termux/termux-x11/actions/workflows/debug_build.yml) 中下载构建产物，并安装`termux-x11-*-debug.apk`（根据设备`architecture`选择，如果不确定，可以选择通用版本）以及`termux-companion packages`构建产物中的`*.deb`文件（如果您使用Termux的`pkg`包管理器）或`*.tar.xz`文件（如果您使用Termux的`pacman`包管理器）。请不要尝试将`shell-loader-nightly.apk`作为Android应用程序安装，它不是为了安装而设计的，仅供使用chroot的用户使用。
或者，您可以从仓库中安装夜间版本的配套包，通过执行`pkg in x11-repo && pkg in termux-x11-nightly`命令来完成安装。

## 运行图形应用程序
您可以通过以下方式启动您所需的图形应用程序：
```
~ $ termux-x11 :1 -xstartup "dbus-launch --exit-with-session xfce4-session"
```
or
```
~ $ termux-x11 :1 &
~ $ env DISPLAY=:1 dbus-launch --exit-with-session xfce4-session
```
如果您使用的是除 Xfce 以外的桌面环境，您可以替换`xfce4-session`

`dbus-launch`对一些用户不起作用，因此您可以使用以下方式启动会话：
```
~ $ termux-x11 :1 -xstartup "xfce4-session"
```

如果您已完成使用Termux:X11，只需通过展开Termux:X11的通知，然后在通知抽屉中点击"Exit"即可。但请注意，`termux-x11` 命令仍在运行，不能通过这种方式终止。

由于某些原因，一些设备只输出带有光标的黑屏，而不是正常的输出，因此您应该传递 `-legacy-drawing` 选项。
```
~ $ termux-x11 :1 -legacy-drawing -xstartup "xfce4-session"
```

由于某些原因，一些设备的屏幕颜色显示会出现颠倒的情况，这时您应该传递 `-force-bgra` 选项。
```
~ $ termux-x11 :1 -force-bgra -xstartup "xfce4-session"
```

## 使用 proot 环境
如果您计划使用 proot 来运行该程序，请注意您需要以 --shared-tmp 选项启动 proot/proot-distro。
如果无法传递此选项，请将 TMPDIR 环境变量设置为指向目标容器中与 /tmp 对应的目录。
如果您正在使用 proot-distro，您应该知道可以从 proot 容器内部启动 `termux-x11` 命令。

## 使用 chroot 环境
如果您计划使用 chroot 或 unshare 来运行该程序，您必须以 root 用户身份运行它，并将 TMPDIR 环境变量设置为指向目标容器中与 /tmp 对应的目录。这个目录必须可以从您启动 termux-x11 的 shell 访问，即它必须在相同的 SELinux 上下文中，相同的挂载命名空间中，等等。此外，您还必须设置 `XKB_CONFIG_ROOT` 环境变量，使其指向容器的 /usr/share/X11/xkb 目录，否则您会遇到与 `xkbcomp` 相关的错误。您可以从[最后一个成功构建](https://github.com/termux/termux-x11/actions/workflows/debug_build.yml) 的工件中获取夜间构建加载器。
```
export TMPDIR=/path/to/chroot/container/tmp
export CLASSPATH=$(/system/bin/pm path com.termux.x11 | cut -d: -f2)
/system/bin/app_process / com.termux.x11.CmdEntryPoint :0
```

### 日志
如果您需要从 `com.termux.x11` 应用程序获取日志，请将 `TERMUX_X11_DEBUG` 环境变量设置为 1，如下所示：
`TERMUX_X11_DEBUG=1 termux-x11 :0`

通过这种方式获取的日志可能会很长。
最好是立即将命令的输出重定向到一个文件中。

### 通知
在 Android 13 中，对后台通知进行了限制，因此您需要明确允许 Termux:X11 显示通知给您。
<details>
<summary>视频</summary>

[img_enable-notifications.webm](https://user-images.githubusercontent.com/9674930/227760411-11d440eb-90b8-451e-9024-d5a194d10b16.webm)

</details>

选项设置:
您可以通过三种方式访问偏好设定菜单：
<details>
<summary>当没有客户端连接时，通过在主屏幕上点击“PREFERENCES”按钮，您可以访问设定菜单。</summary>

![image](./.github/static/1.jpg)
</details>
<details>
<summary>如果通知中提供了“Preferences”按钮，您可以通过点击该按钮来访问选项设置。</summary>

![image](./.github/static/2.jpg)
</details>
<details>
<summary>通过长按启动器中的 `Termux:X11` 图标，并点击“Preferences”应用快捷方式，您可以访问偏好设置。 </summary>

![image](./.github/static/3.jpg)
</details>

## 切换键盘
只需按下“返回”按钮即可。

## 触摸手势
### 触摸板模拟模式
在触摸板模拟模式下，您可以使用以下手势：
* 轻触触摸板一次，模拟鼠标左键的单击操作。
* 连续轻触触摸板两次，模拟鼠标左键的双击操作。
* 使用两根手指同时轻触触摸板，模拟鼠标右键的单击操作。
* 使用三根手指同时轻触触摸板，模拟鼠标中键的单击操作（在某些系统中可能用于滚动或执行其他特定功能）。
* 使用两根手指在触摸板上垂直滑动，模拟鼠标滚轮的垂直滚动操作，用于在文档中上下滚动。
* 使用两根手指在触摸板上水平滑动，模拟鼠标滚轮的水平滚动操作，用于在支持水平滚动的界面上左右移动。
* 使用三根手指在触摸板上向下滑动，用于显示或隐藏额外的按键栏。

### 触摸屏模拟模式。
在模拟触摸屏模式下，您可以使用以下手势：
* 单次轻触屏幕，模拟鼠标左键的单击操作。
* 长按屏幕不放，模拟鼠标的左键按下并持续按住的操作。
* 连续两次轻触屏幕，模拟鼠标左键的双击操作。
* 同时使用两根手指轻触屏幕，模拟鼠标右键的单击操作。
* 同时使用三根手指轻触屏幕，模拟鼠标中键的单击操作。
* 使用两根手指在屏幕上垂直滑动，模拟鼠标滚轮的垂直滚动操作。
* 使用两根手指在屏幕上水平滑动，模拟鼠标滚轮的水平滚动操作。
* 使用三根手指在屏幕上向下滑动，用于显示或隐藏额外的按键栏。。

## 字体或缩放比例太大了！
一些应用程序可能会在DPI方面与X服务器存在问题。请参考 https://wiki.archlinux.org/title/HiDPI 页面了解如何覆盖应用程序特定的DPI或缩放设置。

您可以在窗口管理器设置中修复这个问题（在 Xfce 和 LXQt 的情况下，可以通过“应用程序菜单” > “设置” > “外观” 进行操作）。查找 DPI 值，如果它被禁用了，就启用它并调整其值，直到字体大小合适为止。
<details>
<summary> 截图 </summary>

![image](./.github/static/dpi-scale.png) 
</details>

此外，您还可以使用  `-dpi` 选项启动 `termux-x11`。
```
~ $ termux-x11 :1 -xstartup "xfce4-session" -dpi 120
```

## 使用第三方应用程序
可以使用 Termux:X11 与第三方应用程序配合使用。
查看 shell-loader/src/main/java/com/termux/x11/Loader.java 的工作原理。

# 许可证
已根据 [GPLv3 许可证](https://www.gnu.org/licenses/gpl-3.0.html) 发布
