title: 用chroot的在android上跑linux图形应用程序
speaker: 王奥丞
url: http://soappt.a1ex.wang
transition: cards
theme: light
files: /js/demo.js,/assets/main.css

[slide]
# OpenTUOS - android上跑linux图形应用程序
## - 王奥丞

[slide]
## 目标
------ 
- 将android变成一个生产力工具, 不只是一个手机操作系统 {:&.rollIn}
- android-studio, opengl应用程序 
- Demo

[slide]
## Linux GUI App在桌面Linux上如何运行1
------
![X server architecture picture](/assets/x-architecture.png)

[slide]
## Linux GUI App在桌面Linux上如何运行2
------
1. 动态库问题 {:&.fadeIn}
1. X.org的X Server实现复杂难以复用, 自己实现X11 Protocol?
1. 直接操作framebuffer, 或者直接访问driver, 移植性差

[slide]
## Wayland Architecture
- ![Wayland-Cpu](/assets/cpu.png) {:&.fadeIn}
- 放弃XWindow的架构, 用Wayland的架构
- 用Android提供API来实现显示和input事件处理

[slide]
## chroot + archlinux
- 修改当前进程和子进程的根目录 {:&.fadeIn}
- 在该环境下安装archlinux
- arch和android共享内核和/proc, /dev, /sys少数几个目录
- 解决动态库问题
- 新的根目录
  ```

    /             -> Original /data/arch/chroot
    |-- dev       -> Original /dev
    |-- proc      -> Original /proc
    |-- sys       -> Original /sys
    |-- mnt       -> Original /
        |-- data  -> Original /data
    |-- bin
    |-- etc
    |-- usr
    |-- lib
    ...

  ```

[slide]
## Weston + image-backend
------
- Weston是wayland协议的一个实现, 支持多种后端fb, drm... {:&.rollIn}
- 添加了一个自定义compositor, image-backend
- 用pixman库, 用cpu做composite
- 将composite好的framebuffer写入文件
- 读unix socket, 解包protobuf, 发送鼠标键盘时间给wayland client

[slide]
## App
-----
- 定时读取/tmp/image.bin内容, 显示到SurfaceView {:&.rollIn}
- 重写MainActivity的onTouch方法发送, 把消息用protobuf编码发送到unix socket

[slide]
-----
## GPU support
- ![Wayland-GPU](/assets/gpu.png) {:&.rollIn}
- 多次拷贝frame buffer, 可以优化

[slide]
## In the future
------
- 将一个Linux GUI应用程序对应成为一个Android App {:&.rollIn}
- 修改Android的SurfaceView, 让weston直接写到SurfaceView的buffer中, 减少一次内存拷贝

[slide]
## Q&A
Thanks <br/> 
My github: https://github.com/a1exwang
