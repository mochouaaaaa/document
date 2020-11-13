## ffmpeg操作

#### rtsp文件推流

- -re 以本地帧频读数据，主要用于模拟捕获设备
- -i 指定文件
- -vcodec copy -codec copy 流复制(可以修改码流libx264等)
- -f 指定协议类型
- -rtsp_transport tcp 使用tcp方式连接输出
- rtsp://127.0.0.1:8554/live/rtsp_test 流媒体服务器地址
- -stream_loop -1 无限循环重复推送
- -b:v 1000k  码率
- -r 40 帧数

ffmpeg -re -r 40 -stream_loop -1 -i svoltadv.mp4 -b:v 1000k -vcodec copy -codec copy -f rtsp -rtsp_transport tcp rtsp://0.0.0.0:8554/live/rtsp_test





#### ffplay播放

- ffplay -i rtsp://0.0.0.0:8554/live/rtsp_test