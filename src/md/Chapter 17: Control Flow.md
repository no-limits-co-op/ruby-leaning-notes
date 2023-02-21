# 第 17 章 流程控制

## 17.1 节 `if/elsif/else` 和 `end`

Ruby 为分支逻辑提供了预期的 `if` 和 `else` 表达式，以 `end` 关键字终止：

```ruby
# Simulate flipping a coin
result = [:heads, :tails].sample
if result == :heads
 puts 'The coin-toss came up "heads"'
else
 puts 'The coin-toss came up "tails"'
end
```

在 Ruby 中，if语句是计算值的表达式，结果可以分配给变量:

```ruby
status = if age < 18
           :minor
         else
           :adult
         end
```

Ruby 还提供了 C 风格的三元运算符（详见此处），可以表示为：

```ruby
some_statement ? if_true : if_false
```

上面的例子可以写成这样：

```ruby
status = age < 18 ? :minor : :adult
```

此外，Ruby 还提供了 `elsif` 关键字，该关键字接受表达式以启用其他分支逻辑：

```ruby
label =  if shirt_size == :s
           'small'
         elsif shirt_size == :m
           'medium'
         elsif shirt_size == :l
           'large'
         else
           'unknown size'
         end
```

如果 `if` / `elsif` 中的任何条件都不为 true，并且没有 else 分支，则表达式的计算结果为 `nil`。这在字符串插值中很有用，因为 `nil`。`to_s` 是空字符串：

```ruby
"user#{'s' if @users.size != 1}"
```

## 17.2 节 Case 表达式

Ruby 使用 `case` 关键字切换不同的语句。

正如Ruby官方文档描述的：
case 语句包含一个可选条件，位于 case 参数的位置，有一个或者多个 when 语句。第一个 when 匹配条件（或者计算之后值为布尔真值），那么执行满足条件的代码块，case 语句的值是成功执行 when 子句的值，没有匹配到，则值为 `nil`。

case 语句能以 else 子句结束。每一个 when 语句可以有多个条件，条件之间用逗号隔开。

例子：

```ruby
case x
when 1,2,3
 puts "1, 2, or 3"
when 10
 puts "10"
else
 puts "Some other number"
end
```

简化之后：

```ruby
case x
when 1,2,3 then puts "1, 2, or 3"
when 10 then puts "10"
else puts "Some other number"
end
```

case 子句的值使用 `===` 方法（`not==`）与每个 when 子句匹配。因此，它可以用于各种不同类型的对象。

case 语句可搭配 Range 使用：

```ruby
case 17
when 13..19
 puts "teenager"
end
```

case 语句可搭配正则使用：

```ruby
case "google"
when /oo/
 puts "word contains oo"
end
```

case 语句可搭配 Proc 或者 lambda 表达式使用：

```ruby
case 44
when -> (n) { n.even? or n < 0 }
 puts "even or less than zero"
end
```

case 语句可搭配 Class 表达式使用：

```ruby
case x
when Integer
 puts "It's an integer"
when String
 puts "It's a string"
end
```

通过实现 `===` 方法，你可以自定义 match 类

```ruby
class Empty
 def self.===(object)
   !object or "" == object
 end
end

case ""
when Empty
 puts "name was empty"
else
 puts "name is not empty"
end
```
case 语句可以在没有要匹配的值的情况下使用：

```ruby
case
when ENV['A'] == 'Y'
 puts 'A'
when ENV['B'] == 'Y'
 puts 'B'
else
 puts 'Neither A nor B'
end
```

case 语句有一个值，因此可以将其用作方法参数或赋值：

```ruby
description = case 16
              when 13..19 then "teenager"
              else ""
              end
```

## 17.3 节 真值和假值

在 Ruby 中，有两个值被认为是 `falsy`，当作为 if 表达式的条件进行判断时，将返回 `false`。他们是：
- `nil`
- boolean `false`

其他所有的值被认为是 `truthy`，包括：

- `0`  数字0
- `""` 空字符串
- `\n` 换行符
- `[]` 空数组
- `{}` 空 hash

以接下来的代码为例：

```ruby
def check_truthy(var_name, var)
 is_truthy = var ? "truthy" : "falsy"
 puts "#{var_name} is #{is_truthy}"
end

check_truthy("false", false)
check_truthy("nil", nil)
check_truthy("0", 0)
check_truthy("empty string", "")
check_truthy("\\n", "\n")
check_truthy("empty array", [])
check_truthy("empty hash", {})
```

将会输出

```ruby
false is falsy
nil is falsy
0 is truthy
empty string is truthy
\n is truthy
empty array is truthy
empty hash is truthy
```

## 17.4 节 内联（inline）if/unless

常见的用法是在一行的结尾，使用 if 或者 unless：

```ruby
puts "x is less than 5" if x < 5
```

这被称为条件修饰符，是添加简单保护代码和提前返回的简便方法：

```ruby
def save_to_file(data, filename)
 raise "no filename given" if filename.empty?
 return false unless data.valid?
 File.write(filename, data)
end
```

不能向这些修饰符添加 else 子句。此外，通常不建议在主逻辑中使用条件修饰符——对于复杂代码，应该使用普通 if、elsif 和 else。

# 17.5 节 while 和 until

当符合条件时，while 循环将被执行：

```ruby
i = 0
while i < 5
 puts "Iteration ##{i}"
 i +=1
end
```

当条件为 false 时，until 循环将被执行：

```ruby
i = 0
until i == 5
 puts "Iteration ##{i}"
 i +=1
end
```

## 17.6 节 触发器 `..` 操作符

触发器 `..` 运算符在条件语句中的两个条件之间使用：

```ruby
(1..5).select do |e|
 e if (e == 2) .. (e == 4)
end
# => [2, 3, 4]
```

条件的计算结果为 false，直到第一部分变为 true。然后计算为 true，直到第二部分变为 true。之后，它再次切换为 false。
此示例说明了所选内容：

```ruby
[1, 2, 2, 3, 4, 4, 5].select do |e|
 e if (e == 2) .. (e == 4)
end
# => [2, 2, 3, 4]
```

触发器运算符 `..` 仅在 ifs（包括unless）和三目运算符内部工作。否则，它将被视为范围运算符。

```ruby
(1..5).select do |e|
 (e == 2) .. (e == 4)
end
# => ArgumentError: bad value for range
```

它可以多次从 false 切换到 true 切换（从前向后）：

```ruby
((1..5).to_a * 2).select do |e|
 e if (e == 2) .. (e == 4)
end
# => [2, 3, 4, 2, 3, 4]
```

## 17.7 节 `=||` / 条件赋值

ruby 有或等运算符，当变量的值为 `nil` 或 `false` 时，指定值将被赋值給变量。

```ruby
 ||=  # this is the operator that achieves this.
```

此运算符使用双管表示或，等号表示赋值。你可能认为它代表着这样的东西：

```ruby
x = x || y
```

上述的例子是不对的，或等实际表示的是这样的。

```ruby
 x || x = y
```
如果 x 的值为 `nil` 或者 `false`，那么 `y` 的值被赋给 `x`，否则左侧不发生任何变化。


这里是 `||=` 运算符的一个实际用例。假设您的代码中有一部分需要发送电子邮件给用户。不论出于何种原因，该用户没有电子邮件，你会怎么办。你可以这样写：

```ruby
if user_email.nil?
 user_email = "error@yourapp.com"
end
```

使用 or equals运算符，我们可以剪切整个代码块，提供干净、清晰的控制和功能。

```ruby
user_email ||= "error@yourapp.com"
```
在 false 为有效值的情况下，必须注意不要意外覆盖它：

```ruby
has_been_run = false
has_been_run ||= true
#=> true
has_been_run = false
has_been_run = true if has_been_run.nil?
#=> false
```

## 17.8 节 unless

一个常见的语句 `if !(some conditions)`。Ruby 提供了 `unless` 语句来替代。

语法结构和 if 类似，只是条件为假值，同时，`unless` 不支持 `elsif`，但支持 `else`。

```ruby
# Prints not inclusive
unless 'hellow'.include?('all')
 puts 'not inclusive'
end
```

## 17.9 节 throw、catch

不像其他许多语言，ruby语言中的throw和catch关键字与异常处理不相关。

在ruby中，throw和catch的表现有点像其他语言中的label关键字。它们通常被用来改变流程控制，但和错误与异常的概念不相关。

```ruby
catch(:out) do
 catch(:nested) do
   puts "nested"
 end
   puts "before"
 throw :out
   puts "will not be executed"
end
puts "after"
# prints "nested", "before", "after"
```

## 17.10 节 三目运算符
ruby有三目运算符?:，它基于判断条件为真值返回两个值中的一个：
conditional ? value_if_truthy : value_if_falsy

value = true
value ? "true" : "false"
#=> "true"
value = false
value ? "true" : "false"
#=> "false"
三目运算符表现和写 if a then b else c end一样。
例子：
puts (if 1 then 2 else 3 end) # => 2
puts 1 ? 2 : 3 # => 2
x = if 1 then 2 else 3 end
puts x # => 2

## 17.11 节 循环控制中的break,next,redo
ruby 代码块中的执行流可能被 break， next， redo 语句中断。

- **break**
break 语句会立即退出循环，当前循环中，在其之后代码会直接跳过，整个迭代会终止。

```ruby
actions = %w(run jump swim exit macarena)
index = 0
while index < actions.length
  action = actions[index]
  break if action == "exit"
  index += 1
  puts "Currently doing this action: #{action}"
end
# Currently doing this action: run
# Currently doing this action: jump
# Currently doing this action: swim
```
- **next**
`next` 语句，会立即回到迭代的开头，在其之后代码会直接跳过，然后执行下一次迭代。


```ruby
actions = %w(run jump swim rest macarena)
index = 0
while index < actions.length
 action = actions[index]
 index += 1
 next if action == "rest"
 puts "Currently doing this action: #{action}"
end
# Currently doing this action: run
# Currently doing this action: jump
# Currently doing this action: swim
# Currently doing this action: macarena
```

- **redo**
redo 语句会立即回到当前迭代的开头，然后重试当前迭代，在其之后语句会被忽略。

```ruby
actions = %w(run jump swim sleep macarena)
index = 0
repeat_count = 0
while index < actions.length
 action = actions[index]
 puts "Currently doing this action: #{action}"
 if action == "sleep"
 repeat_count += 1
 redo if repeat_count < 3
 end
 index += 1
end
# Currently doing this action: run
# Currently doing this action: jump
# Currently doing this action: swim
# Currently doing this action: sleep
# Currently doing this action: sleep
# Currently doing this action: sleep
# Currently doing this action: macarena
```

- Enumerable 迭代
除了循环之外，像 `each`、`map` 这些方法可以使用可枚举的迭代方法：

```ruby
[1, 2, 3].each do |item|
 next if item.even?
 puts "Item: #{item}"
end
# Item: 1
# Item: 3
```

代码块的结果值
在break和next语句中，可以用一个值来接收代码块的结果值：

```ruby
even_value = for value in [1, 2, 3]
 break value if value.even?
end
puts "The first even value is: #{even_value}"
# The first even value is: 2
```

## 17.12 节 return 和 next 的比较：代码块中的 no-local return
看一下这个代码片段：

```ruby
def foo
 bar = [1, 2, 3, 4].map do |x|
 return 0 if x.even?
 x
 end
 puts 'baz'
 bar
end
foo # => 0
```

可能期望返回 `map` 的块结果值（一个数组）。所以 `foo` 的返回值应该是 `[1,0,3,0]`。相反 return 返回了一个值。注意没有打印出 `'baz'`，这表示没有执行到这一行。

next 就可以达到想要的效果，它更像一个块级的 return

```ruby
def foo
 bar = [1, 2, 3, 4].map do |x|
 next 0 if x.even?
 x
 end
 puts 'baz'
 bar
end
foo # baz
 # => [1, 0, 3, 0]
```

在没有返回的情况下，块返回的值是其最后一个表达式的值。

## 17.13 节 begin，end

begin 块是一个组合多条语句的控制结构

```ruby
begin
 a = 7
 b = 6
 a * b
end
```

begin 块返回最后一条语句的值。以下例子返回 `3`：

```ruby
begin
 1
 2
 3
end
```

begin 对于使用 `||` 操作符的条件赋值非常有用，有时候需要多条语句来计算并返回一个结果。

```ruby
circumference ||=
  begin
    radius = 7
    tau = Math::PI * 2
    tau * radius
  end
```

它也可以和其他块级结构结合，例如：rescue，ensure，while，if，unless 等块级结构，以便更好地支持程序流程控制。

## 17.14 节 逻辑语句控制流

这似乎有些违反直觉，你可以使用逻辑运算符来决定是否运行一条语句。

```ruby
File.exist?(filename) or STDERR.puts "#{filename} does not exist!"
```

它会检查文件是否存在，如果不存在，则打印错误信息。`or` 语句是惰性的，这意味着，只要第一个条件成立，它就不会执行。但第一个条件不成立时，就必须检查第二个。

常见的应用是设置默认值：

```ruby
glass = glass or 'full' # Optimist!
```

上述代码：假如没有设置 glass，那么久设置成 full。除此之外，你还可以用以下方式：

```ruby
glass ||= 'empty' # Pessimist.
```

也可以只要第一个语句为 false，就运行第二个语句：

```ruby
File.exist?(filename) and puts "#{filename} found!"
```

同样地， `and`也是惰性的，它只有在需要的时候才会执行。

`or` 的运算优先级比 `and` 低。类似地，`||` 的优先级比 `&&` 低。符号形式的优先级比词句形式的。当它和赋值语句混用时，你就知道了。

```ruby
a = 1 and b = 2
#=> a==1
#=> b==2
a = 1 && b = 2; puts a, b
#=> a==2
#=> b==2
```
注意 ruby 推荐的代码风格：

`and` 和 `or` 关键字被禁用了，最小的阅读性是不值得引入微妙的高概率 bug 的。对于布尔表达式，通常使用 `&&` 和 `||`。对于流程控制，使用 `if` 和 `unless`；`&&` 和 `||` 也是可以的，但不够清晰。
