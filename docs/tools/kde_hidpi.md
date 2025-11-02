# KDE HiDPI屏幕配置方法

kde-plasma-desktop(5:142)系统设置中的显示器配置已经提供了全局缩放的功能，整体效果还是不错的，但是在终端、编辑器等某些软件中还是会出现小数缩放倍数导致的黑线与白线，很难让人忽略。

参考了网上的资料，发现直接修改字体配置中的`固定字体DPI`数值并且修改默认字体大小后可以达到比较好的显示效果，不会在出现白线。

但是chromium或者基于chrome内核的程序依然存在缩放导致的显示不协调问题，最终发现，将相关程序的启动参数中传入QT相关的环境变量即可达到比较好的缩放效果,例如将chromium的启动命令修改为如下命令

```
Exec=env QT_SCALE_FACTOR=1.75 XMODIFIERS=@im=fcitx GTK_IM_MODULE=fcitx QT_IM_MODULE=fcitx /usr/bin/chromium %U
```

上述修改中还包含了fcitx相关的配置，可以解决输入法无法正常使用的奇怪问题。

kde 6可以不进行上述设置了，因为没有修改固定字体dpi的设置了，系统对高分屏的缩放效果也比较完美(我将缩放设置为145%之后依然会在某些地方出现白线，但是改为140%和150%就一切正常)，对于chromium可以进行如下设置解决输入问题。

```
Exec=/usr/bin/chromium -enable-features=UseOzonePlatform -ozone-platform=wayland --enable-wayland-ime --wayland-text-input-version=3 %U
```

对于微信，可以做如下设置解决缩放和输入问题。

```
Exec=env QT_AUTO_SCREEN_SCALE_FACTOR=1 XMODIFIERS=@im=fcitx GTK_IM_MODULE=fcitx QT_IM_MODULE=fcitx /usr/bin/wechat %U
```
