# RSI_sensor_data_reading
# 简介  
## 使用步骤  
    1.参照手册使用iDAS软件进行传感器配置  
    2.用自带的网线连接到电脑  
    3.设置电脑的IP  
    4.执行编写好的代码  
  传感器使用公司软件配置，一般购买传感器的时候都会送配置软件、配置教程、配置专用参数，而且教程都是傻瓜式的，所以本文不再讲解。  
## python编写客户端 
需要导入两个关键的包 
```
import struct 
import socket 
```
python没有专门处理字符的模块(字符被当做字符串），所以需要使用struct模块对数据进行解码，不懂struct模块的可以参照python之struct详解。socket是python的重要的通讯模块(套节字）,可以快速建立TCP通讯。  
## 创建客服端，建立连接 
```
IP_ADDR	= '192.168.0.108' 
PORT = 4008 
#创建连接插口 
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM) 
#连接 
s.connect((IP_ADDR, PORT)) 
``` 
通过socket函数建立插口函数，通过connect建立连接，其中IP和端口号在传感器配置阶段可以设置，也可以直接使用默认的IP。要使用程序采集多个传感器信息时，我们需要设置传感器的端口号，如下： 
```
#检查当前端口号
AT+ETPT=?\r\n   #发送
ACK+ETPT=4008$OK\r\n  #回应
#修改当前端口号
AT+ETPT=4009\r\n   #发送
ACK+ETPT=4009$OK\r\n  #回应
```
## 查看和配置参数  
查看和配置参数需要遵循特定的格式，可以参照手册 
```
#查看连接地址
sendData = "AT+EIP=?\r\n"
s.send(sendData)
recvData = bytearray(s.recv(1000))
print recvData
```
发送一个需要查看的参数命令，接收返回的数据，打印出来就可以了。 
```
#设置采样频率
set_update_rate = "AT+SMPR=100\r\n"
s.send(set_update_rate)
recvData = bytearray(s.recv(1000))
print recvData
```
设置参数也是一样的，参照手册发送设置命令就可以了。 
## 接收六轴力传感器并解码  
### 首先设置需要传感器上传的数据格式，上传的方式等 
```
#连续上传数据包
get_data_stream = "AT+GSD\r\n"
s.send(get_data_stream)
```
### 然后使用一个循环接收数据 
```
while True:
    data = s.recv(1000)
```
###  最后是采用解码数据，采用单精度float默认对其方式解码 
```
    fx = struct.unpack('f', data[6:10])[0]
    fy = struct.unpack('f', data[10:14])[0]
    fz = struct.unpack('f', data[14:18])[0]
    mx = struct.unpack('f', data[18:22])[0]
    my = struct.unpack('f', data[22:26])[0]
    mz = struct.unpack('f', data[26:30])[0]
```
### 如果想让传感器停止上传数据 
```
#停止数据
stop_data_stream = "AT+GSD=STOP\r\n"
s.send(stop_data_stream)
recvData = bytearray(s.recv(1000))
print str(recvData)
```
# 完整代码见 Singal_SRI_sensor.py 程序
