# RSI_sensor_data_reading
此项目旨在利用python程序读取RSＩ六轴力传感器数据
使用步骤：

  1 参照手册使用iDAS软件进行传感器配置
  2 用自带的网线连接到电脑
  3 设置电脑的IP
  4 执行编写好的代码
  传感器使用公司软件配置，一般购买传感器的时候都会送配置软件、配置教程、配置专用参数，而且教程都是傻瓜式的，所以本文不再讲解。

Python写TCP客户端
  需要导入两个关键的包
  import struct
  import socket
