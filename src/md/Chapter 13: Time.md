# 第 13 章 Time

## 13.1 节 如何使用 `strftime`

在 Ruby 中将时间转换为字符串是很常见的事情。 `strftime` 是将时间转换为字符串的方法。
以下是一些例子：

```ruby
Time.now.strftime("%Y-%m-d %H:%M:S") #=> "2016-07-27 08:45:42"
```

还可以更简单

```ruby
Time.now.strftime("%F %X") #=> "2016-07-27 08:45:42"
```

## 13.2 节 创建 `time` 对象

获取当前时间:

```ruby
Time.now
Time.new # is equivalent if used with no parameters
```

获取指定时间：

```ruby
Time.new(2010, 3, 10) #10 March 2010 (Midnight)
Time.new(2015, 5, 3, 10, 14) #10:14 AM on 3 May 2015
Time.new(2050, "May", 3, 21, 8, 16, "+10:00") #09:08:16 PM on 3 May 2050
```

使用 `to_i` 将时间转换成时间戳：

```ruby
Time.now.to_i # => 1478633386
```

使用 `at` 将时间戳转回时间

```ruby
Time.at(1478633386) # => 2016-11-08 17:29:46 -0200
```
