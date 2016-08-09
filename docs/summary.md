# newinstaller with OTA

# newinstaller with OTA功能需求和计划文档
内容：
- 项目简介
- 功能需求
- 项目进展
- 存在问题

# 项目简介
本项目是对用户下载到本地OTO新版本进行本地滚动更新.
目前基于newinstaller实现.

### 当前开发人员（2016.08.01-2016.08.31）
陈渝: 系统重启进行本地ota更新(基于newinstaller)

### 以往开发人员
肖络元:完成初步框架

# 功能需求
## 基本功能
### OTA newinstaller界面的基本元素
- 界面: 由于在初始启动中没有图形界面,目前基于字符模式的窗口界面进行交互设计
- 语言: 由于是字符模式的窗口界面,设计语言是英文
-
### OTA installer界面的基本操作
- 确认更新本地OTO系统新版本的交互窗口:oto系统启动后,发现了本地特定目录下有OTO系统新版本,需要用户确认是否更新.如果不更新,继续执行.
- 确认更新完毕后重启的交互窗口:newinstaller完成更新后,提示用户重启.用户选择重启后,系统重启.

## 详细操作
### OTA newinstaller基本操作详细说明
|序号|操作|描述|
|---|---|---|
|1|启动某oto版本(如OPENTHOS-2016-08-08)|挂载OTO分区,启动kernel,执行init,init会执行update_detect()
|2|查找oto系统更新|update_detect()分析/mnt下的多个oto安装目录/mnt/android*/,查找/mnt/android*/data/media/0/System_OS/update文件
|3|分析update文件|如果有update文件,则分析其内容,倒数第二行是oto系统更新文件名,倒数第一行: "1"需要更新,其他为不需要更新
|4|查找oto系统更新文件|查找到此文件存在且需要更新,执行do_update()
|5|分析oto系统更新文件|分析oto系统更新文件内容,如何满足更新要求,则提示用户是否更新,如果用户选择更新,则执行update_to()
|6|完成oto系统更新文件内容复制|复制oto系统更新文件中的关键内容到/mnt/androidnewest下,拷贝当前oto系统的data目录,清除缓存,把update改为update.old
|7|提示重启|完成上一步后,弹出窗口,提示重启.

## 接口说明
### update文件和oto系统更新文件的固定目录
- /data/media/0/System_OS/

### update文件说明
- 倒数第一行内容:
  - "1": 需要更新系统.一般由ota app设
  - "0": 系统更新完毕.一般由上次启动的newinstaller设置,表示系统更新完成
  - "-1":用户拒绝更新.一般由上次启动的newinstaller设置,表示用户拒绝更新
  - "-2":系统更新失败.一般由上次启动的newinstaller设置,表示系统更新失败
  

- 倒数第二行内容:要下载镜像文件真实的md5值。当用户将镜像文件下载成功后，将用update文件中的
- 镜像文件真实md5值与从本地获取下载后镜像文件的md5值进行校验：如果校验成功，提示镜像文件校
- 验成功，显示安装更新系统的按钮。如果校验失败，提示镜像文件校验失败，自动删除update和系统
- 镜像文件，退出程序


- 倒数第三行内容:oto系统更新文件名字.此文件要求与update文件在一个固定目录下
- 但newinstaller完成更新后,会把update改名为update.old. ota app可根据此判断上次启动已经完成更新

### oto系统更新文件说明
- 要求是openthos的iso格式的文件,即通过```make iso_img```得到的android_*.iso文件

### 更新后的安装目录
- 更新后的系统都统一放在固定的/mnt/androidnewest目录下
- 用户可以通过grub/uefi的启动菜单的"OPENTHOS Newest"选项进行选择 

## 相关文档
- [OTA app设计开发文档](https://github.com/openthos/appstore-ota-analysis/blob/master/OTA-analysis.md)
- [OTA已有设计文档](https://github.com/openthos/appstore-ota-analysis/blob/master/Server/OTA.md)

# 项目进展
| 计划 | 完成 | 开始时间 | 结束时间 |其他
|---|---|---|---|---|
| 实现newinstaller with OTA框架 |40%|2016-07-06|2016-08-01|肖络元
| newinstaller with OTA改进 |60%|2016-08-02|~|陈渝

# 存在问题

- 当用户提示不更新时,需要采用某种方式避免再次启动后还是要让用户选择是否更新
- 偶尔出现更新后,系统重启后,在图形界面转圈或显示"启动应用",一直持续(在qemu中比较常见)
- 与ota app还需加强沟通
- 各种情况的测试不够
- 目前是英文,缺少中文界面
- 目前基于字符方式,缺少图形界面
