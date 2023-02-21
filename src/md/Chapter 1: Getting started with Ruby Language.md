# Chapter 1：Ruby入门

版本发布日期

| 2.5.1 | 2018-03-28 |
| --- | --- |
| 2.4 | 2016-12-25 |
| 2.3 | 2015-12-25 |
| 2.2 | 2014-12-25 |
| 2.1 | 2013-12-25 |
| 2.0 | 2013-02-24 |
| 1.9 | 2007-12-25 |
| 1.8 | 2003-08-04 |
| 1.6.8 | 2002-12-24 |

## Section 1.1：Hello World

<aside>
💡 假设你已经安装了ruby运行环境

</aside>

将以下代码写入一个名叫hello.rb的文件

```ruby
puts 'Hello World'
```

在命令行中敲入一下命令执行文件中的ruby代码

```bash
$  ruby hello.rb
```

将输出

```
Hello World
```

控制面板将立即展示输出，ruby源文件在执行前不需要编译。ruby代码解释器在运行时进行编译和执行。

## Section 1.2：作为自执行文件—使用类似Shabang（仅限类Unix操作系统）实现Hello World

在脚本文件中增加一条解析指令（Shabang）。创建一个名叫hello_world.rb的文件， 文件包含以下内容。

```ruby
#!/usr/bin/env ruby
puts 'Hello World'
```

给脚本配置可执行权限， 以下是在Unix中如何配置可执行权限

```bash
$ chmod u+x hello_world.rb
```

现在不需要明确的调用ruby代码解释器来执行脚本了。

```bash
$ ./hello_world.rb
```

## Section 1.3：IRB中实现Hello World

或者， 可以使用IRB（Interactive Ruby Shell）来立即执行之前写在ruby文件中的ruby语句

键入以下内容开启ruby会话

```bash
$ irb
```

接下来输入以下命令

```ruby
puts 'Hello World'
```

运行结果展示在控制台的下一行（包含新一行）

```
Hello World
```

如果不想新起一行， 使用print

```ruby
print 'Hello World'
```

## Section 1.4：不需源文件实现Hello World

在安装ruby之后，运行以下命令，这表明不需要创建ruby源文件也能执行简单的ruby程序

```bash
ruby -e 'puts "Hello World"'
```

也能向ruby代码解释器的标准输入放入ruby代码，[文档](https://en.wikipedia.org/wiki/Here_document)介绍了一种在shell命令行实现方法

```bash
$ ruby <<End
puts "Hello World"
End
```

## Section 1.5：Tk中实现Hello World

Tk是ruby标准的图形用户界面（GUI），它为ruby程序提供了跨平台的GUI

```bash
require 'tk'
TkRoot.new{ title: "Hello World" }
Tk.mainloop
```

结果如下

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ba817f1c-ffb0-40b4-9743-fc13bf5959d2/Untitled.png)

逐步解析如下：

1. 加载tk程序包

```bash
require "tk"

```

1. 定义一个title为Hello World的对话框

```bash
TkRoot.new{ title: "Hello World" }
```

1. 启动主循环并显示对话框

```bash
Tk.mainloop
```

## Section 1.6：第一个ruby方法

### 概览

创建一个名叫my_first_method.rb的文件， 将以下代码写入文件中

```ruby
def hello_world
	puts "Hello World"
end

hello_world() # 或者只写hello_world(不写括号)
```

在命令行执行以下命令

```bash
ruby my_first_method.rb
```

命令行中将输出以下内容

```
Hello Wrold
```

**解析**

- 在本例中，def是一个定义方法的关键字， hello_world是方法名
- puts “Hello World”  是向控制台的管道输出字符串Hello World
- end 标明hello_world方法结束的关键字
- 由于hello_world方法不接收任何参数，可以省略括号来调用方法