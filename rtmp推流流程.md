

## 使用

1. 获取一个key

   - GET  http://{ip}:8090/control/get?room=movie

     ```json
     {
         "status": 200,
         "data": "rfBd56ti2SMtYvSgD5xAV0YU99zampta7Z7S575KLkIZ9PYk"
     }
     ```

     

2. 推流

   - 命令行

     ```shell
     sudo ffmpeg -re -i 视频文件名称.flv  -vcodec copy -acodec copy -f flv rtmp://192.168.4.143:1935/{设备编号}/rfBd56ti2SMtYvSgD5xAV0YU99zampta7Z7S575KLkIZ9PYk
     ```

     

   3.界面播放，也可以找支持rtmp协议的播放器

​			rtmp://localhost:1935/{设备编号}/movie