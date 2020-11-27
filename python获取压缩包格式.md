#### 获取tar格式压缩包格式并返回解压参数

- ```python
  def detect_compression(fname):
      """
      获取解压文件的参数
   
      :param fname: 文件绝对路径
      :return:
      """
      with open(fname, 'r', encoding='ISO 8859-1') as f:
          header = f.read(263)
  
      print(header[0: 2])
      if header[0: 2] == 'BZ':
          print('-jxf .tar.bz2')
          return ['-jxf']
  
      elif ''.join([hex(ord(c)) for c in header[0: 2]]) == '0x1f0x8b':
          print('-zxf .tar.gz')
          return ['-zxvf']
  
      elif header[1: 5] == '7zXZ' and ord(header[0]) == 253:
          print('-Jxf .tar.xz')
          return ['-Jxf']
  
      elif header[1: 5] == '7zXZ' and ord(header[0]) != 253:
          print('--lzma -xf .tar.lzma')
          return ['--lzma', '-xf']
  
      elif header[257: 262] == 'ustar':
          print('-xf .tar')
          return ['-xf']
  
      raise TypeError('未获取文件类型')
  ```

  以上代码参考lxd源码解压镜像方法