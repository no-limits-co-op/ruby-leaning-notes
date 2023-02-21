# 第 11 章 字符串

## 11.1 节 双引号字符串和单引号字符串的区别

主要区别在于，双引号字符串支持插值和全套字符串转义。

例如，双引号字符串可以通过插值包含任意 Ruby 表达式：

```ruby
# Single-quoted strings don't support interpolation
puts 'Now is #{Time.now}'
# Now is #{Time.now}
```
```ruby
# Double-quoted strings support interpolation
puts "Now is #{Time.now}"
# Now is 2016-07-21 12:43:04 +0200
```

双引号字符串支持全套字符串转义，包括 `\n`, `\t`

```ruby
puts 'Hello\nWorld'
# Hello\nWorld
puts "Hello\nWorld"
# Hello
# World
```
虽然单引号字符串不支持转义序列，但保留了使单引号字符串有用所需的最小集合：文字单引号和反斜杠，分别为 `\'` 和 `\\`。

## 11.2 节 创建字符串

ruby提供了多种创建String对象的途径。最常见是的是用单引号或者双引号创建字符串字面量。

```ruby
s1 = 'Hello'
s2 = "Hello"
```

主要的区别在于--双引号字符串更加灵活，支持插值和转义字符。

还有其他的方式创建字符串：可以使用任意的字符串分隔符。其中的一种方式是使用 `%` 紧跟着一对字符串分隔符：

```ruby
%(A string) %{A string}
%<A string>
%|A string| %!A string!
```

也可以使用 `%q` 和 `%Q` 紧跟着一对分隔符，区别和 `'` 和 `"` 一样：

```ruby
puts %q(A string)
# A string
puts %q(Now is #{Time.now})
# Now is #{Time.now}
puts %Q(A string)
# A string
puts %Q(Now is #{Time.now})
# Now is 2016-07-21 12:47:45 +0200
```

如果字符串中包含 `'`、`"` 或者二者都包含时，`%q` 和 `%Q` 就非常有用了，也不需要进行转义。

```ruby
%Q(<a href="/profile">User's profile<a>)
```

只要其后跟着的分隔符是成对出现的，可以使用多种不同的分隔符

```ruby
%q(A string)
%q{A string}
%q<A string>
%q|A string|
%q!A string!
```

## 11.3 节 大小写转换操作

```ruby
"string".upcase # => "STRING"
"STRING".downcase # => "string"
"String".swapcase # => "sTRING"
"string".capitalize # => "String"
```

这四个方法不会修改原始值：

```ruby
str = "Hello"
str.upcase # => "HELLO"
puts str # => "Hello"
```

下面的这四个相近的方法， 但是会修改原始值：

```ruby
"string".upcase! # => "STRING"
"STRING".downcase! # => "string"
"String".swapcase! # => "sTRING"
"string".capitalize! # => "String"
```

举个例子来看：

```ruby
str = "Hello"
str.upcase! # => "HELLO"
puts str # => "HELLO"
```

注意： 在ruby 2.4 版本之前，这些方法不处理 Unicode 字符

## 11.4 节 字符串拼接

字符串使用 `+` 进行拼接

```ruby
s1 = "Hello"
s2 = " "
s3 = "World"
puts s1 + s2 + s3
# => Hello World
s = s1 + s2 + s3
puts s
# => Hello World
```

或者使用 `<<` 操作符进行拼接：

```ruby
s = 'Hello'
s << ' '
s << 'World'
puts s
# => Hello World
```

需要注意的是：`<<` 会修改操作符左边对象的值。

也可以使用 `*` 来重复字符串，例如：

```ruby
"wow" * 3
# => "wowwowwow
```

## 11.5 节 字符串的位置

在ruby中，字符串可以左对齐（left-justified）、右对齐（right-justified）或者居中（centered）。
对于左对齐而言，使用 `ljust` 方法，方法接收两个参数， 整数表示新字符串的长度，可选字符串表示剩余填充内容。
假如整数比源字符串的长度大，新字符串将左对齐，剩余部分将使用可选字符串填满， 如果未给出可选字符串，则默认使用空格填充。

```ruby
str ="abcd"
str.ljust(4)  # "abcd"
str.ljust(10) # "abcd      "
```

对于右对齐而言，使用 `rjust` 方法，方法接收两个参数， 整数表示新字符串的长度，可选字符串表示剩余填充内容。
假如整数比源字符串的长度大，新字符串将右对齐，剩余部分将使用可选字符串填满， 如果未给出可选字符串，则默认使用空格填充。

```ruby
str = "abcd"
str.rjust(4)  # "abcd"
str.rjust(10) # "      abcd"
```

对于居中对齐而言，使用 `center` 方法，方法接收两个参数， 整数表示新字符串的长度，可选字符串表示剩余填充内容。新字符串将被源字符串填充，且源字符串处于新字符串的中心。

```ruby
str = "abcd"
str.center(4)  # "abcd"
str.center(10) # "   abcd   "
```

## 11.6 节 字符串拆解

`String#split` 基于分割符将字符拆解成数组。

```ruby
"alpha,beta".split(",")
# => ["alpha", "beta"]
```

空字符串，拆解的之后得到空数组

```ruby
"".split(",")
# => []
```

没有匹配到分割符，拆解之后得到长度为1且包含元素是字符串的数组：

```ruby
"alpha,beta".split(".")
# => ["alpha,beta"]
```

也可以基于正则表达式拆解字符串：

```ruby
"alpha, beta,gamma".split(/, ?/)
# => ["alpha", "beta", "gamma"]
```

分割符是可选参数，默认值为空格（即默认基于空格拆解字符串）：

```ruby
"alpha beta".split
# => ["alpha", "beta"]
```

## 11.7 节 字符串的开头
判断字符串以某种方式开头，`start_with?` 方法就派上用场了：

```ruby
str = "zebras are cool"
str.start_with?("zebras") # => true
```

还可以使用 `index` 判断某种方式的开始的位置：

```ruby
str = "zebras are cool"
str.index("zebras").zero? => true
```

## 11.8 节 连接成字符串

`Array#join` 基于分隔符将数组连接成字符串。

```ruby
["alpha", "beta"].join(",")
# => "alpha,beta"
```

分隔符是可选参数，默认值是空字符串串：

```ruby
["alpha", "beta"].join
# => "alphabeta"
```

空数组，无论是否传入分隔符，连接的结果都是空字符串

```ruby
[].join(",")
# => ""
```

## 11.9 节 字符串插值

双引号（`"`）和 `%Q` 通过 `#{ruby_expression}` 支持插值。

```ruby
puts "Now is #{Time.now}"
# Now is Now is 2016-07-21 12:47:45 +0200
puts %Q(Now is #{Time.now})
# Now is Now is 2016-07-21 12:47:45 +0200
```

## 11.10 节 字符串的结尾

`end_with?` 用来判断字符串以某个方式结尾。

```ruby
str = "I like pineapples"
str.end_with?("pineaaples") # => false
```

## 11.11 节 字符串格式化

ruby 中通过占位符的形式，将一组数据插入到字符串中

```ruby
"Hello %s, my name is %s!" % ['World', 'br3nt']
# => Hello World, my name is br3nt!
```

在上述代码中，占位符是两个 `%s`，值是数组 `['World', 'br3nt']`，通过 `%` 运算符向字符串中注入值。

## 11.12 节 字符串占位符的替换

```ruby
p "This is %s" % "foo"
# => "This is foo"
p "%s %s %s" % ["foo", "bar", "baz"]
# => "foo bar baz"
p "%{foo} == %{foo}" % {:foo => "foo" }
# => "foo == foo
```

更多细节见 `String %` 和 `Kernel::sprintf`。

## 11.13 节 多行字符串

创建多行字符串的最简单方法是在引号之间写入多行：

```ruby
address = "Four score and seven years ago our fathers brought forth on this
continent, a new nation, conceived in Liberty, and dedicated to the
proposition that all men are created equal."
```

但她的主要问题是，如果字符串包含引号，它将破坏字符串语法。 要解决此问题，可以改用heredoc：

```ruby
puts <<-RAVEN
 Once upon a midnight dreary, while I pondered, weak and weary,
 Over many a quaint and curious volume of forgotten lore—
 While I nodded, nearly napping, suddenly there came a tapping,
 As of some one gently rapping, rapping at my chamber door.
 "'Tis some visitor," I muttered, "tapping at my chamber door—
 Only this and nothing more."
 RAVEN
```
Ruby 支持带有 `<<EOT` 的 shell 风格的 here 文档，但终止文本必须另起一行。 这会使得代码缩进混乱，所以没有太多理由使用这种风格。 需要注意的是，字符串是否有缩进，取决于代码本身的缩进方式。

ruby 2.3 版本之后，通过引入 `<<~` 去除多余的开头空格来解决这个问题：

```ruby
def build_email(address)
 return (<<~EMAIL)
   TO: #{address}
   To Whom It May Concern:
   Please stop playing the bagpipes at sunrise!

   Regards,
   Your neighbor
 EMAIL
end
```

%字符串也可以用来创建多行字符串

```ruby
%q(
HAMLET Do you see yonder cloud that's almost in shape of a camel?
POLONIUS By the mass, and 'tis like a camel, indeed.
HAMLET Methinks it is like a weasel.
POLONIUS It is backed like a weasel.
HAMLET Or like a whale?
POLONIUS Very like a whale
)
```
有以下几种方式避免插值和转义：
- 使用单引号，而不是双引号：`'\n is a carriage return.'`
- 使用 `%q` ：`%q[#{not-a-variable}]`
- heredoc中以单引号结尾：
```ruby
<<-'CODE'
 puts 'Hello world!'
CODE
```

## 11.14 节 字符串中替换字符

`tr` 方法返回一个字符串的副本，其中第一个参数的字符被第二个参数的字符替换。

```ruby
"string".tr('r', 'l') # => "stling"
```

仅仅用另一个表达式替换表达式的第一次出现，请使用 sub 方法

```ruby
"string ring".sub('r', 'l') # => "stling ring"
```

如果需要替换所有匹配到的表达式，请用 `gsub`

```ruby
"string ring".gsub('r','l') # => "stling ling"
```

删除匹配到的表达式，请用空格作为第二个参数。

也可以使用正则表达式作为上述方法的第一个参数。

需要注意的是，这些方法只会返回一个字符串的新副本，并且不会就地修改字符串。如果需要修改源字符串，请相应使用 `tr!`、`sub!` 和 `gsub!` 方法。

## 11.15 节 理解字符串中的数据
在 Ruby 中，字符串只是一个字节序列以及编码名称（例如 UTF-8、US-ASCII、ASCII-8BIT），用于指定如何将这些字节解释为字符。

Ruby 中字符串可用于保存文本（基本上是字符序列），在这种情况下，通常使用 UTF-8 编码格式。

```ruby
"abc".bytes # => [97, 98, 99]
"abc".encoding.name # => "UTF-8"
```

Ruby 中字符串也可用于保存二进制数据（字节序列），在这种情况下，通常使用 ASCII-8BIT 编码。

```ruby
[42].pack("i").encoding # => "ASCII-8BIT"
```

如果尝试使用的字符串中的字节序列与编码不匹配，，则会导致错误。

```ruby
"\xFF \xFF".valid_encoding? # => false
"\xFF \xFF".split(' ') # ArgumentError: invalid byte sequence in UTF-8
```
