获取推流的设备号，组成推流地址

- device_number 设备编号
- domian 域名

rtsp://{domain}/flow/{device_number}





拉流地址

协议头可变

rtsp://{domain}/{device_number}/movie



如果开启双路推流(标清和高清同时)那么可以在device_name后面以":"冒号拼接一个串口的ID或者以某一个参数区分是标清还是高清

