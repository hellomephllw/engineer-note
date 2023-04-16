https://www.liaoxuefeng.com/wiki/1252599548343744/1298613246361634

SimpleDateFormat不是线程安全，不要使用static

java.util.TimeZone不能使用UTC，可以使用GMT+8

时区的转换有相对性
绝对时间戳 + 时区A => 绝对时间戳 + 时区B
DateTimeStr + 时区A => DateTimeStr + 时区B

offset是时区偏移量

Date对象有几个严重的问题：它不能转换时区，除了toGMTString()可以按GMT+0:00输出外，Date总是以当前计算机系统的默认时区为基础进行输出。此外，我们也很难对日期和时间进行加减，计算两个日期相差多少天，计算某个月第一个星期一的日期等。

Calendar也无法转换时区