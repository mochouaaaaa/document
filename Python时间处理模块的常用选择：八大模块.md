## 时间数据

时间格式是数据类型中基础也不容忽视的一类。不像整数那样大道至简也不像字符串那样包罗万象，却独有魅力，时间数据本身除了加减、比较运算外，也有下周、去年、时区等更专项的时间切换。在各类编程语言里都提供时间对象的支持，在MySQL里也有DATETIME类型。商业里的DAU、GMV、LTV也少不了时间限定和时间属性，因此数据分析时少不了对时间数据类型的处理与转换。
 Python通过套件time、datetime、timeit处理时间类型数据，但面对一些情况时会不够灵活和易用，在时间序列生成和截断方面捉襟见肘，于是诞生了Arrow、Pendulum、Maya等库增强了Python的时间处理能力。本篇对4个标准库和6大第三方模块进行介绍，在面对需求时能拿到最趁手的工具。

![img](https:////upload-images.jianshu.io/upload_images/2473543-bfcb723492b9b8b5.PNG?imageMogr2/auto-orient/strip|imageView2/2/w/735/format/webp)

本文所用库概览

## 模块概览

在Python中进行时间类型数据处理能用到的模块有：

- time：Python内置时间库，通过时间戳或元组表示时间；
- datetime：内置日期库，处理日期时间对象和属性；
- dateutil：基于datetime库的实用拓展，增强了对时间间隔和时间序列的处理；
- pd.Timestamp：pandas库用于时间处理的类；
- Arrow：优秀的Python时间库，简化了时间类型数据的解析和输出；
- Pendulum：可以和Arrow对标的时间处理库，pendulum意为钟摆；
- Delorean：在dateutil基础上进一步拓展的时间库，以《回到未来》中的时间旅行车命名；
- moment：灵感来源于Moment.js，目前相对原始；
- Maya：和Arrow等库对标，增强了对时区的处理，有调用pendulum的部分功能；

在深入这些库的使用之前，先补充一些先验知识：
 epoch：时间基准点至特定时间的总秒数，一般用一个浮点数值记录，这个基准点在Unix及类Unix系统中是格林威治时间1970年01月01日00时0分0秒，因此也称为Unix时间戳(Timestamp)。因为地球是一个椭球体，当英国是中午时中国北京已经在吃晚饭了，不同经度地区的0点相对于格林威治的0点有一个时差，也就有时区(timezone)的区分，以UTC(世界协调时)作为基准，中国采用的东八区就可表示为UTC+8，对应北京时间减8个小时就是UTC时间。
 基于以上需要考虑的问题，在时间类中，表示一个时间有两种基本选择：
 一是用浮点数记录一个时间戳epoch，时间小于1970年则是负数，二是用元组或字典记录年月日时分秒时区等，在Python的time模块就是记录了epoch和一个元组叫struct_time，这两者之间可以互相转换。

## 模块特性与实践

### time&datetime

time是Python内置的时间库，功能简约但实用，通常和同为内置库的datetime、pytz及calendar互相配合解决各类时间表示、计算、输出等需求。
 time的常用方法有：

- time.time()：得到当前时间戳Timestamp，是一个浮点数；
- time.localtime([secs])：将一个时间戳转换为当前时区的struct_time。secs参数未提供，则以当前时间为准，相当于获取当前时间now()；
- time.gmtime(ts)：时间戳转struct_time；struct_time是一个包含了9个元素的元组，对应着改时间对象的年月日、本年第几天等属性；
- time.mktime(t)：struct_time转时间戳；
- time.strftime("%Y-%m-%d",t)：struct_time转格式化字符串；
- time.strptime('2020-12-7',"%Y-%m-%d")：字符串转struct_time；



```python
import time
time.time() #type(time.time())==float
#Out[]:1607319973.764
time.localtime()
# time.struct_time(tm_year=2020, tm_mon=12, tm_mday=7, tm_hour=13, tm_min=46, tm_sec=13, tm_wday=0, tm_yday=342, tm_isdst=0)
st=time.gmtime(time.time())
st.tm_year #获取属性，st是元组，不能修改
# 2020
```

基于time模块生成的时间对象t，如果是时间戳形式表示的，是不能直接得到t是在哪一年等属性的，需要先转struct_time形式，然后就可以写st.tm_year获取所在年。st是元组，不能修改，即不能用st.tm_year=2019来修改的st的实际值。



```python
t=time.strptime('2020-12-7 13:52:15',"%Y-%m-%d %H:%M:%S")
# time.struct_time(tm_year=2020, tm_mon=12,...)
time.strftime("%Y-%m-%d %H:%M:%S",t)
# 2020-12-7 13:52:15
```

从文件中读取数据时常需要从字符串形式变成时间对象，就会用到str**p**time，是string parse time的简写，即从字符串数据类型中解析成时间类型。str**f**time是把时间类型格式化为字符串，是str**p**time的逆操作，f是format的缩写。
 时间类型格式化有一套特定的占位符，下面介绍的符号在其他时间模块里也通用，因此常用的占位符还是需要心里有数才能灵活“组装”出自己需要的字符串效果的。下面表格列出了常用的时间格式化占位符，更全面的表可查阅[time模块文档](https://links.jianshu.com/go?to=https%3A%2F%2Fdocs.python.org%2F3%2Flibrary%2Ftime.html)。

![img](https:////upload-images.jianshu.io/upload_images/2473543-101e3ec6bda21e02.PNG?imageMogr2/auto-orient/strip|imageView2/2/w/464/format/webp)

常用时间格式化符号

time模块常和datetime模块组合使用，time侧重在时间，datetime在日期方面方法更丰富，且datetime会和pytz及calendar配合处理时间对象。
 在datetime里也有strftime和strptime，不过需要注意的是，两个库输入参数顺序的区别，datetime的strftime，格式化字符串在后，代码实例如下。



```python
from datetime import datetime
dt=datetime.strptime('2020-12-7 13:52:15',"%Y-%m-%d %H:%M:%S")
datetime.strftime(dt,"%Y-%m-%d %H:%M:%S") #
# 2020-12-7 13:52:15
time.strftime("%Y-%m-%d %H:%M:%S",t)
# datetime库内部也是调用time的striptime
# datetime.strftime -> _wrap_strftime ->_time.strftime
```

在datetime中新建时间对象可以直接使用`datetime(y, m,d,tzinfo)`输入参数，用`datetime.now()`获得当前时间，通过`datetime.fromtimestamp(ts)`可以将时间戳ts转为时间对象，生成的datetime时间对象在获取属性时用到的语句类似`dt.year`，有year/month/day/hour/second/tzinfo等可以用。tzinfo是时区属性，datetime在时区相关处理时通常用到pytz。



```python
import pytz
sh=pytz.timezone('Asia/Shanghai') #新建一个时区
dt=datetime(2020,12,7,hour=8,tzinfo=sh)
datetime.fromtimestamp(time.time())
#datetime.datetime(2020,12,8,16,59,42,797401)
dt.year #返回给定datetime对象的年份
#Out[]: 2020
#属性有.hour .minute .second .microsecond 等
datetime.weekday() #返回星期几，星期一为 0，星期天为 6
#方法还有 .isoweekday() .toordinal() 等
datetime.combine(dt.date(),dt.time()) 
#combine：将一个date对象和一个time对象组合成一个datetime对象
from datetime import timezone #如果不使用pytz库
d1=datetime(2020, 11, 21,tzinfo=timezone(timedelta(hours=8)))
tdt=dt-d1
# datetime.timedelta(days=16)
dt+timedelta(20)
```

两个datetime日期相减得到的是一个时间间隔对象(imedelta)，timedelta可以和数值进行乘法和整除运算，两个timedelta对象之间可以进行加减运算，但不能比较大小，datetime对象可以和timedelta对象进行加减得到新的datetime实现时间偏移。
 datetime也会和内置的calendar库进行配合，顾名思义，calendar库主要用来处理和输出整年、整月的日历。



```python
print(calendar.calendar(2020)) #打印2020年日历
#calendar.prcal(2020) #两个语句效果相同
calendar.prmonth(2019,2) #打印2019年2月的日历
calendar.isleap(2020) #是否闰年
# True
calendar.weekday(2020,11,20) #指定日期为星期几，
#4 代表星期五
```

![img](https:////upload-images.jianshu.io/upload_images/2473543-3d4b703635d2485f.PNG?imageMogr2/auto-orient/strip|imageView2/2/w/546/format/webp)

calendar

这几个库其他的实用方法有：

- time.sleep(secs)：线程推迟指定的时间运行，单位为秒；
- time.asctime([t]) ：把一个表示时间的元组或者struct_time表示为这种形式：'Sun Jun 20 23:21:05 1993'，如没有参数，将会将time.localtime()作为参数传入；
- time.ctime([secs])：把一个时间戳（按秒计算的浮点数）转化为time.asctime()的形式。如果参数未给或者为None的时候，将会默认time.time()为参数。它的作用相当于time.asctime(time.localtime(secs))；
- calendar.leapdays(n,m)：年份n到m之间的闰年数量；

### dateutil

dateutil模块是基于datetime库的实用拓展，增强了对时间间隔和时间序列的处理，因此dateutil类型直接继承了datetime类型，[dateutil库](https://links.jianshu.com/go?to=https%3A%2F%2Fdateutil.readthedocs.io%2Fen%2Fstable%2F)生成的时间对象就是datetime。Anaconda下该库已经安装，模块里有parser、easter、relativedelta、rrule等实用类进行时间处理。



```python
import dateutil #anaconda下已经安装，直接import
dt=dateutil.parser.parse('April 29 2020 14:20')
#可以从字符串解析，不需要手动写匹配的占位符。
dt=dateutil.parser.parse('April 29') #会取当前年
# datetime.datetime(2020, 4, 29, 0, 0)
dt=dateutil.parser.parse("Today is January 1, 2047 at 8:21:00AM", fuzzy_with_tokens=True)
```

dateutil的parser类用于更方便地从字符串解析为datetime对象，`parser.parse(string)`可以从各种类型的字符串例如一句自然语言中解析出日期，但输入的参数string必须是字符串，输入时间戳不行（这个和下面提到的Arrow等库不同）。
 因为解析为datetime类型的对象，所以可以使用datetime的各种方法和属性，例如需要知道是哪一年仍然使用`dt.year`获取。
 一些datetime类的方法可以基于dt实例使用，要实现从时间戳转时间对象，就可以使用`dt.fromtimestamp(ts)`，获取当前时间，就可以使用`dt.now()`。



```python
dt.fromtimestamp(dt.timestamp()) #时间戳与时间对象互转
dt.strftime('%Y-%m-%d') #只能输入一个参数
#时间对象转字符串
dateutil.easter.easter(2020,method=3) 
#计算输入年份复活节的日期
```

dateutil计算时间间隔的方法封装在relativedelta里，通过输入参数months等明确间隔的时间距离，tz用于处理时区。



```python
dt+dateutil.relativedelta.relativedelta(months=1, weeks=1)
#时间偏移
# datetime.datetime(2021, 1, 14, 14, 15, 39, 173204)
relativedelta(datetime(2003, 10, 24, 10, 0),dt) #得到一个时间间隔
relativedelta(NOW, johnbirthday) #得到一个人的年龄
#下周五对应的时间
dt+relativedelta(weekday=FR)
```

rrule类用于生成和处理一个时间序列。rrule的主要参数有：

- freq：声明序列重复的周期；
- count：生成多少个时间对象；
- dtstart：开始的时间点；



```python
list(dateutil.rrule.rrule(freq=dateutil.rrule.MONTHLY, count=4, dtstart=datetime(2020, 12,7)))
# [datetime.datetime(2020, 12, 7, 0, 0),datetime.datetime(2021, 1, 7, 0, 0),...]
list(dateutil.rrule.rrulestr("""
    DTSTART:20201207T090000
    RRULE:FREQ=DAILY;INTERVAL=10;COUNT=4
    """))
#效果同上，rrulestr是根据字符串规则生成时间序列
```

以上例子生成的是一个由4个时间对象组成的序列，开始时间是2020年12月7号，每月重复一条记录。rrule.rrulestr()是把字符串输入当参数。

### pandas

实际在进行数据分析时，通常都会用到pandas库却不一定会导入datetime等库，而pandas模块也提供了Timestamp、Timedelta等类用于时间类型数据的处理转换。直接使用pd.Timestamp也更容易进行广播运算。

pandas的Timestamp对象用法和datetime库基本一致，各种`dt.year`属性都有，也有`dt.isleapyear`用于判断是否是闰年。pd.Timedelta对应datetime的timedelta，表示时间间隔。



```python
df['时间']=pd.to_datetime(df['dt']) 
df['years']=df['时间'].apply(lambda x:x.year)
sdf=df.loc[df['years']==2018]
ddr=dd/(pd.Timestamp('2018-12-31')-pd.Timestamp('2018-1-1')).days 
df['tfs']=df['时间'].apply(lambda x:x.hour+x.minute/60+x.second/3600)
```

《[用pandas处理时间格式数据](https://links.jianshu.com/go?to=https%3A%2F%2Fmp.weixin.qq.com%2Fs%2Fz74hYBJGkO_oHgjNLgiDhQ)》讲述了一个处理Excel文件中时间数据的案例。

### Arrow

Arrow是一个优秀的Python时间处理库，现在其他有追求的第三方时间处理库都喜欢在文档里对标Arrow，足矣见Arrow的影响力。[Arrow](https://links.jianshu.com/go?to=https%3A%2F%2Farrow.readthedocs.io%2Fen%2Fstable%2F)通过收束接口增强了其易用性，可以快速上手使用，get统筹各种输入的解析，replace负责各种时间要素的修改，format解决各类格式化输出的需求，range处理时间序列生成问题。
 Arrow解析字符串或datetime对象得到的是一个自定义时间对象，通过dt.time、dt.datetime、dt.timestamp等将时间数据从Arrow内置对象转为time等库的时间对象，一些例子如下。



```python
import arrow #在Anaconda下已经安装
arrow.get('2020-12-08 17:31:20') 
#Out[]: <Arrow [2020-12-08T17:31:20+00:00]>
dt=arrow.get(1607334506) #get可输入Unix时间戳，也可输入datetime对象
dt.datetime #转为dateime类型
dt.naive #转为当地时区的datetime类型
dt.floor('hour') #从小时处截断，小时之后的数清零
d1.replace(hour=3)
d1.shift(weeks=+4) #当前时间4周后
d1.to('Asia/Shanghai') #换时区
dt.format('YYYY-MM-DD') #输出格式化字符串
arrow.Arrow.range('hour',arrow.now(),arrow.now().shift(hours=5))
#arrow生成时间序列
dt.humanize() #dt的自然语言表示
```

Arrow的具体用法可参考前文《[Python处理时间数据的另一种选择，在标准库之外](https://www.jianshu.com/p/150327d5c935)》。

![img](https:////upload-images.jianshu.io/upload_images/2473543-313827e21cd7c21e.PNG?imageMogr2/auto-orient/strip|imageView2/2/w/1179/format/webp)

arrow库用法概览

### Pendulum

Pendulum也是一款很优秀的Python时间处理模块，其内置数据类型拓展自datetime，与datetime有着很好的兼容性。Pendulum比dateutil功能更丰富，足矣和Arrow对标。Arrow的易用性体现在接口简洁，Pendulum的易用性表现在很多datetime的方法都兼容，而且[Pendulum](https://links.jianshu.com/go?to=https%3A%2F%2Fpendulum.eustace.io%2F)的文档页面也更美观漂亮。Pendulum[ˈpendʒələm]意为钟摆，是很好的时间意向。Pendulum通过其内置的DateTime对象实现和拓展datetime.datetime的功能，同时封装出Duration、Period及Timezones处理时间偏移、时区、时间序列。



```python
import pendulum
dt=pendulum.now() #获取本地时区的当前时间
#DateTime(2020,12,8,18,0,8,697484,tzinfo=Timezone('Asia/Shanghai'))
pendulum.tomorrow() #明天的这个时候
dt.year # 2020
dt.week_of_year #dt所在周是本年第几周
dt.age #dt对应日期目前的年龄
dt.strftime('%Y-%m-%d')
d2=dt.set(year=2019) #把年份变成2019
dt.add(years=-1) #把时间变成1年前，注意是years不是year
period = pendulum.period(dt, dt.add(days=8))
list(period.range('days',2)) #时间序列
```

其他的一些实用方法如下：

- pendulum.datetime(2020,5,7)：输入年月日等生成DateTime，对应着datetime.datetime()的写法;
- pendulum.today()：获取当天时间， .tomorrow() .yesterday() 等可以用；
- pendulum.local(args)：获取当地时间的对象，可以输入年月日等；
- pendulum.parse(text)：从文本中解析出时间对象，有个类似的方法是pendulum.from_format(text,s)；
- pendulum.from_timestamp(ts)：把时间戳ts转为时间对象；
- dt.int_timestamp：把dt表示为整数的timestamp，对应的还有.float_timestamp；
- pendulum.timezone("Europe/Paris")：生成一个时区对象；
- d2.diff_for_humans(dt)：将时间间隔按自然语言输出；

Pendulum的一些函数需要输入DateTime作为参数时，输入datetime对象也兼容，例如Period时期对象的start、end对象输入DateTime对象或datetime对象都可以，更详细的Pendulum特性可阅读《[挑战Arrow，需要怎样的实力？Pendulum使用笔记](https://www.jianshu.com/p/7ba0466df29e)》。

![img](https:////upload-images.jianshu.io/upload_images/2473543-d4d7283852eb7150.PNG?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

pendulum-xmind

### Delorean

dateutil库在datetime库基础上进行拓展，[Delorean](https://links.jianshu.com/go?to=https%3A%2F%2Fdelorean.readthedocs.io%2Fen%2Flatest%2F)站在dateutil的肩膀上进一步增强了时间处理能力，其接口更偏向面向对象的写法，时间戳使用epoch定义，其时间对象和datetime对象兼容性也很高，并且内置时间对象可以直接和datetime.timedelta进行运算。
 Delorean是《回到未来》中的主角的时间旅行车，作为一个以epoch表示时间的程序库挺契合的。
 Delorean抽象了多个接口用于解析和转换其他格式数据为时间对象，解析字符串用parse、处理时间戳用epoch、输入的是datetime对象直接用Delorean()。获取对象的年月日等属性，需转datetime再使用datetime的接口。



```python
from delorean import Delorean
dt=Delorean() #获取当前时间，相当于now
dt=delorean.parse('2020/12/07')
dt.datetime.year #获取年份
dt.replace(hour=8) #改时间
dt.shift('US/Eastern') #改时区
dt - timedelta(hours=2) #两小时之前
list(delorean.stops(freq=delorean.DAILY,count=10))
```

Delorean修改时间要素是用replace，而改时区是使用的shift。除了用stops生成时间序列外，还有range_daily()、range_hourly()等快速方法生成每天或每小时的时间序列。Delorean和datetime的协作很方便，但接口不够简洁和成体系，获取属性还需要转为datetime，显得常用的功能却没有优先封装，与Arrow、Pendulum等库还有些差距，是一个值得了解的Python时间库，详细了解其用法可看前文《[设定基准点去时间旅行|Delorean使用笔记](https://www.jianshu.com/p/82e6e52464ba)》。

![img](https:////upload-images.jianshu.io/upload_images/2473543-27a4ff4257cfd464.PNG?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

delorean-xmind

### moment

和Arrow类似，moment也是灵感来自Moment.js库。moment是一个在发展中的库，基本功能不缺，但也不是很完善，其文档 建议优先考虑Arrow及Pendulum库。
 moment将数据的输入封装在moment.date里，在解析能力上，比Arrow的get更进一步，例如get传入tomorrow或者2 weeks ago是会报错的，这是arrow的get还不支持的写法，但moment.date可以解析。



```python
import moment
moment.date('2020-12-07 14:20:10')
#<Moment(2020-12-07T14:20:10)>
moment.date("2 weeks ago")
dt=moment.date("December 18, 2020")
moment.unix(1355875153626)
dt.year #获取dt所在年份
```

moment的时间对象也是自定义的对象，获取其属性使用`dt.year`的写法，和其他库一致，进行时间偏移用的add和subtract方法，同时也有replace的接口，而且写`dt.replace(day=2)`或者`dt.replace(days=2)`都没出问题。输出格式化的字符串使用format。通过`dt.datetime`转为dateime类型，而输出时间戳是用`dt.epoch()`方法。



```python
dt=moment.now() #还有utcnow()可以用
dt.add(days=2) #.subtract()
dt.replace(day=5)
dt.replace(days=5)
dt.format('YYYY-MM-DD')
dt.datetime #转datetime对象
```

moment目前的接口还是偏少，生成一个时间序列目前还不能实现。
 使用moment时，一个小问题是用`pip install moment`可能会安装不上，需要通过`pip install moment --user` 去安装。

### Maya

Maya站在datetime、pendulum、snaptime等模块的肩膀上发展有一定特色的时间处理能力，[Maya](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Ftimofurrer%2Fmaya)自定义对象MayaDT也是通过epoch定义时间，能很好地避免一些时区问题。
 Maya的时间创建能力上排名前列，有丰富的接口用于从各种数据中解析出时间对象，when和parse可以从一些自然语言字符串中解析出时间要素，这方面和moment不遑多让，例如写`maya.when('tomorrow')`和`.when('2 weeks ago')`等；当然从time/datetime对象、时间戳转Maya对象也是没有压力。



```python
import maya
maya.when('tomorrow') #明天的这个时候,直接从自然语言转MayaDT
maya.parse('2020-12-08T03:15') #字符串转maya时间对象
dt=maya.now() #获取当前时间
maya.MayaDT.from_datetime(datetime.now()) #datetime对象转MayaDT
maya.MayaDT.from_struct(time.gmtime()) 
maya.MayaDT(1606533154) #时间戳转Maya时间对象
dt.from_iso8601(text) #从符合ISO-8601标准的字符串中解析时间
```

在输出和转换方面，有`dt.datetime()`方法将MayaDT对象转为datetime对象，也能直接通过`dt.year`获取MayaDT对象的属性，有`dt.iso8601()`输出满足ISO-8601标准的时间字符串，和from_iso8601相对应。几个优秀库都有的输出为自然语言功能在Maya里封装为`dt.slang_time()`，并且还有slang_date也能使用，slang是俚语的意思。



```python
dt=maya.when('2020, 12, 7')
dt.slang_time()
# '8 hours ago'
dt.add(days=10).slang_time()
# 'in 1 week'
list(maya.intervals(start=maya.now(),
                    end=maya.now().add(days=1),
                    interval=60*60))
#生成start到end的每小时间隔的时间值序列
```

Maya的很多方法调用了其他时间库，例如dt.year等属性用了datetime库、snap方法是调用了snaptime库、parse和add用到了Pendulum库，很多需求Maya没有自己去造轮子，同时也显得依赖项有些多，要深入了解Maya的用法可以翻看前文《[博采众长穿梭时空|Maya库使用笔记](https://www.jianshu.com/p/42731308e075)》。

![img](https:////upload-images.jianshu.io/upload_images/2473543-762975c7005bf3fc.PNG?imageMogr2/auto-orient/strip|imageView2/2/w/1117/format/webp)

maya库概览

在程序运行方面，除了时间数据本身，量测代码运行时间、模拟特定时间环境，都是编程语言层的使用场景。在Python中，timeit库用于量测一段代码的运行时间，即可以方便地计算代码跑一次的耗时，也能计算多次重复运行的平均耗时，在进行代码评测时小巧实用。[FreezeGun](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fspulec%2Ffreezegun) 是在进行测试时常用的时间库，主要应用场景是做测试时保证输入的一致性；功能是调用freeze_time后，程序运行返回的时间就是冻结所在的时间，相当于测试任务是在那个时间运行的。



```python
from timeit import timeit
timeit('x=1')  #看执行1000000次x=1的时间
def func():
    s=[i for i in range(1000)]
    return s
timeit('func()', number=1) #执行函数func 一次的时间
from timeit import repeat
#repeat和timeit用法相似，多了一个repeat参数，表示重复测试的次数(可以不写，默认值为3.)，返回值为一个时间的列表。
t = repeat('func()', 'from __main__ import func', number=100, repeat=5)
#在命令行中使用：
python -m timeit '"-".join(str(n) for n in range(100))'

from freezegun import freeze_time
@freeze_time("2012-01-14")
def test():
    return datetime.now()
test()
#FakeDatetime(2012, 1, 14, 0, 0)
```

![img](https:////upload-images.jianshu.io/upload_images/2473543-baf72a2a160aa0c8.PNG?imageMogr2/auto-orient/strip|imageView2/2/w/539/format/webp)

freezegun的使用效果

## 总结

在数据处理和数据分析过程中，主要需要解决的数据需求有以下几点：

- 生成时间对象，从字符串或者写赋值语句得到一个时间对象；从内置的time/datetime对象转更容易处理的时间对象，如数据列是从Excel读入的，去解析该列为时间对象；
- 对特定时间对象t，获取年月日、分钟等时间要素；
- 时间运算；
  - 时间间隔Timedelta，两个时间对象相减；
  - 一个时间对象+一个差值后得到新的时间对象，例如获取t一周后的时间t2,
- 时间对象转为特定格式的字符串；
- 时间序列的整体移动与抽样；
- 非结构日期处理，从自然语言中解析时间；

各个库解决该需求的方式总结如下表。



![img](https:////upload-images.jianshu.io/upload_images/2473543-0c1397d927c7f97f.PNG?imageMogr2/auto-orient/strip|imageView2/2/w/664/format/webp)

![img](https:////upload-images.jianshu.io/upload_images/2473543-a6d4f62275dd5c64.PNG?imageMogr2/auto-orient/strip|imageView2/2/w/751/format/webp)

