# 第 29 章 文件和输入输出操作

| 标志 | 含义 |
| -- | -- |
| `r` | 只读，从文件的开头开始（默认模式） |
| `r+` | 读写，从文件的开头开始 |
| `w` | 只写，将文件截断为零长度或者创建用来写入的新文件 |
| `w+` | 读写，将文件截断为零长度或者创建用来读写的新文件 |
| `a` | 只写，如果文件存在，则在文件结尾开始，否则创建用来写入的新文件 |
| `a+` | 读写，如果文件存在，则在文件结尾开始，否则创建用来读写的新文件 |
| `b` | 二进制文件模式，在 Windows 上删除 `EOL <-> CRLF`。并将外部编码设置为ASCII-8BIT，除非明确指定。（此标志只能与上述标志一起出现。例如，`File.new(“test.txt”，“rb”)` 将以读模式将 **test.txt** 作为二进制文件打开。） |
| `t` | 文本文件模式，（此标志只能与上述标志一起出现。例如，`File.new("test.txt"，"wt")` 将以只读模式将 **test.txt** 作为文本文件打开。） |

## 29.1 节 向文件写入字符串

字符串可以通过 `File` 类的实例写入文件。

```ruby
file = File.new('tmp.txt', 'w')
file.write("NaNaNaNa\n")
file.write('Batman!\n')
file.close
```

`File` 类也为 `new` 和 `close` 提供了简便的写法 `open`。

```ruby
File.open('tmp.txt', 'w') do |f|
  f.write("NaNaNaNa\n")
  f.write('Batman!\n')
end
```

对于简单的操作，可以直接使用 `File.write` 将字符串写入文件。注意，它默认会覆盖文件。

```ruby
File.write('tmp.txt', "NaNaNaNa\n" * 4 + 'Batman!\n')
```

`File.write` 可以指定文件模式，传入一个 hash值作为参数：

```ruby
File.write('tmp.txt', "NaNaNaNa\n" * 4 + 'Batman!\n', { mode: 'a'})
```

## 29.2 节 从标准输入读取内容

```ruby
# Get two numbers from STDIN, separated by a newline, and output the result
number1 = gets
number2 = gets
puts number1.to_i + number2.to_i
## run with: $ ruby a_plus_b.rb
## or: $ echo -e "1\n2" | ruby a_plus_b.rb
```

## 29.3 节 使用 `ARGV` 读取参数

```ruby
number1 = ARGV[0]
number2 = ARGV[1]
puts number1.to_i + number2.to_i
## run with: $ ruby a_plus_b.rb 1 2
```

## 29.4 节 打开和关闭文件

手动打开和关闭文件。

```ruby
# Using new method
f = File.new("test.txt", "r") # reading
f = File.new("test.txt", "w") # writing
f = File.new("test.txt", "a") # appending

# Using open method
f = open("test.txt", "r")

# Remember to close files
f.close
```

使用代码块，自动关闭文件。

```ruby
f = File.open("test.txt", "r") do |f|
  # do something with file f
  puts f.read # for example, read it
end
```

## 29.5 节 获取输入的单个字符

和 `gets` 不一样， `chomp` 不会等待。

首先将 stdlib 导入。

```ruby
require 'io/console'
```

然后写一个 helper 方法：

```ruby
def get_char
  input = STDIN.getch
  control_c_code = "\u0003"
  exit(1) if input == control_c_code
  input
end
```

按 `control + C`，就会退出。这点非常重要。
