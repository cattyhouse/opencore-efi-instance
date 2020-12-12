# 这是什么

1. 这是一个 OpenCore 的范例
1. 本范例以 Intel 300 系列主板为基础
1. 本范例中, iGPU是作为 Headless 使用的, 通过 AMD 独立显卡来显示.
1. 本范例是纯文字界面, 如果需要用 OpenCore 的图形界面, 自行研究.
1. 如果官方有升级, 本范例会持续更新
1. 只需要做少量修改就可以直接拿来用

# 下载

````
git clone https://github.com/cattyhouse/opencore-efi-instance
````

# 必选设置选项

修改 config.plist

1. 机型

    选取一个你喜欢的机型, 比如 `iMac19,1` 赋值给 `SystemProductName`

1. `序列号` 和 `MLB`
       
    ```sh
    # macOS 上
    ~/Downloads/OpenCore-0.6.4/Utilities/macserial/macserial -g -m iMac19,1
    # windows
    ~/Downloads/OpenCore-0.6.4/Utilities/macserial/macserial,exe -g -m iMac19,1
    # macserial 包含在 OpenCore 官方的下载包里面
    ```
    
    默认会生成 10 组, 选取任意一组, 竖线前面的是序列号, 赋值给 `SystemSerialNumber` ;  后面的是 MLB, 赋值给 `MLB`

1. SystemUUID
    
    可以网上在[线生成UUID](https://www.dute.org/uuid), 赋值给 `SystemUUID`
    
    也可以用系统工具生成, 比如 uuidgen

1. ROM

    其实就是一个网卡的 MAC 地址, 可以用你的网卡上的, 也可以随机生成, 注意去掉 `:` 或者 `-`, 并全部大写, 赋值给 `ROM`

1. 声卡路径
    
    范例中 `DeviceProperties/Add/` 下面的 `PciRoot(0x0)/Pci(0x1f,0x3)` 是我的主板的声卡的路径, 不同的主板, 有所不同, 具体查询方法, :

    ```sh
    gfxutil -f HDEF
    ```
    gfxutil [下载地址](https://github.com/acidanthera/gfxutil/releases) 


# 可选设置选项

修改 config.plist
    
> 对于布尔选项, 当不同的软件编辑 Plist 的时候, 有的软件显示的是 YES, 有的软件显示的是 True, 但他们其实是一个意思, 就是 启用 的意思, NO/FALSE 同理是 禁用 的意思

1. `AAPL,ig-platform-id`
    
    本范例中的 AAPL,ig-platform-id 适用于 UHD630, 且作为 Headless 使用, Headless 的意思是, 不提供显示, 只提供解码等功能, 比如 Safari 硬解 Youtube 4k 的 VP9 格式的视频. 如果需要用 iGPU 提供显示, 需要做修改, 参考 [Intel显卡问答](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.cn.md)

1. `AppleCpuPmCfgLock` `AppleXcpmCfgLock` `AppleXcpmExtraMsrs`

    如果主板可以关闭 CFG LOCK, 这三个选项都可以设置为 NO/FALSE. 作为范例, 我设置的都是 YES/TRUE

1. `XhciPortLimit`

    macOS 限制 USB 的端口数量为 15 个, 通常主板都会超过这个数量 (注意一个 USB3.0 的接口算 2 个端口, 因为它提供 USB2.0 和 USB3.0 支持), 范例中, 此选项设置的是 YES/TRUE. 如果[定制了 USB](https://github.com/headkaze/Hackintool), 可以设置为 NO/FALSE. 

1. `boot-args`

    boot-args 是启动参数, 范例设置了几个常见的, 具体的含义是
    
    1. -v , 看到启动文字信息, 方便排查问题, 可以去掉
    2. keepsyms=1, 启动失败不自动重启, 方便记录问题, 可以去掉
    3. igfxfw=2, 为 intel iGPU 加载 Apple GuC, macOS下可以使用 iGPU 的最高频率. 有的主板可能有bug,可能会导致死机
    4. radgva=1, 给 amd 4xx/5xx 显卡提供 HEVC 的编码解码功能. 有的显卡可能有bug,可能会导致死机


# 更新

如果本范例更新了, 可以 `git pull` 之后, 除了仔细阅读我的 commit 记录之外.

对于 config.plist

1. 用 VsCode 等工具 同时打开本范例和你的 config.plist, 然后选中两者, 做 Compare Selected, 找出有变化的地方.
2. 然后用 Plist Edit Pro, 分别打开两者, 对比更新. 

对于 其他文件, 直接覆盖.