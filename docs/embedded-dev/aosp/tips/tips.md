# AOSP Tips

## 增量编译

我们希望可以只编译修改过的代码，而不是全局编译，可以先执行`m installclean`再执行`m`进行增量编译。

## 使能以太网设置入口

在AOSP 10中以太网设置是根据`ro.vendor.ethernet_settings`这个prop来决定是否使能的，可以在设备属性配置中增加以下配置来使能以太网设置。

```
PRODUCT_PROPERTY_OVERRIDES += ro.vendor.ethernet_settings=true
```

## 截屏

截屏到设备本地

```shell
$ adb shell screencap -p /sdcard/screenshot.png
```

截屏并输出到host

```shell
$ adb exec-out screencap -p > screenshot.png
```
