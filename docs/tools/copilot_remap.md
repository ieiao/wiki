# Copilot按键重映射

用了10年的老笔记本罢工了，于是新购入了一款笔记本，发现右control键变成了Copilot键，而我经常会用到右control键，所以需要对其进行重映射。

使用evtest对Copilot键的键值进行了抓取，日志如下

```
Event: time 1749359916.916862, -------------- SYN_REPORT ------------
Event: time 1749359917.912362, type 4 (EV_MSC), code 4 (MSC_SCAN), value db
Event: time 1749359917.912362, type 1 (EV_KEY), code 125 (KEY_LEFTMETA), value 1
Event: time 1749359917.912362, -------------- SYN_REPORT ------------
Event: time 1749359917.912430, type 4 (EV_MSC), code 4 (MSC_SCAN), value 2a
Event: time 1749359917.912430, type 1 (EV_KEY), code 42 (KEY_LEFTSHIFT), value 1
Event: time 1749359917.912430, -------------- SYN_REPORT ------------
Event: time 1749359917.912546, type 4 (EV_MSC), code 4 (MSC_SCAN), value 6e
Event: time 1749359917.912546, type 1 (EV_KEY), code 193 (KEY_F23), value 1
Event: time 1749359917.912546, -------------- SYN_REPORT ------------
Event: time 1749359917.912786, type 4 (EV_MSC), code 4 (MSC_SCAN), value db
Event: time 1749359917.912786, type 1 (EV_KEY), code 125 (KEY_LEFTMETA), value 0
Event: time 1749359917.912786, -------------- SYN_REPORT ------------
Event: time 1749359917.912883, type 4 (EV_MSC), code 4 (MSC_SCAN), value 2a
Event: time 1749359917.912883, type 1 (EV_KEY), code 42 (KEY_LEFTSHIFT), value 0
Event: time 1749359917.912883, -------------- SYN_REPORT ------------
Event: time 1749359917.912970, type 4 (EV_MSC), code 4 (MSC_SCAN), value 6e
Event: time 1749359917.912970, type 1 (EV_KEY), code 193 (KEY_F23), value 0
```

可以发现，当Copilot键被按下后，键盘上报了`leftshift+leftmeta+f23`组合按键的按下与释放事件，接下来就算一直长按，也不会上报长按事件。

查找了一些资料，并进行了一番尝试，最终使用`keyd`达到了预期效果，使用的keyd配置如下

```
[ids]
*

[main]

leftshift+leftmeta+f23 = timeout(oneshot(copilot_remap), 5000, layer(control))

[copilot_remap]

a = C-a
e = C-e
w = C-w
r = C-r
```

上述配置将在copilot键按下后切换到copilot_remap layer,在copilot_layer定义了需要添加control组合键的映射，这里的配置添加了几个bash的组合键功能，当copilot_remap layer中有任意键按下后会回到默认layer,可以完美的实现我想要的效果。
