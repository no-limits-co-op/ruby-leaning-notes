# 第 28 章 正则表达式及基于运算符的正则

## 28.1 节 `=~` 操作符

```ruby
if /hay/ =~ 'haystack'
  puts "There is hay in the word haystack"
end
```

注意：顺序是非常重要的。尽管 `'haystack' =~ /hay/` 在绝大多数的情况下都是相等的，但是副作用可能不同：

- 只有当 `Regexp#=~` 被调用时（`regexp =~ str`）时，从命名捕获组捕获的字符串才会赋值给局部变量；
- 由于操作数可能是任意对象，对于 `regexp =~ str`，可能是 `Regexp#=~` 或者 `String#=~`。

注意，这不会放回 `true` 或者 `false` 值，它的返回值要么是匹配到的下标，或者在没有找到时返回 `nil`。因为数字在 Ruby 中是真值（包括0），`nil` 是假值，所以它这样写是对的。如果你想要一个布尔值，使用 `#===`，将会在另一个例子里展示。

## 28.2 节 Case 表达式中的正则表达式

你可以使用 `switch` 表达式来检测一个字符串是否匹配多个正则表达式。

```ruby
case "Ruby is #1!"
when /\APython/
  puts "Boooo."
when /\ARuby/
  puts "You are right."
else
  puts "Sorry, I didn't understand that."
end
```

这能正常工作是因为 `case` 使用 `===` 操作符（而不是 `==` 操作符）检测是否相等。当正则表达式在等式的左侧时，使用 `===` 进行比较，它会检测字符串是否匹配。

## 28.3 节 组，命名及其他

Ruby 使用命名组（`?<name>...`）拓展了标准的组语法（`...`）。它允许按名称进行提取，而不必计算有多少个组。

```ruby
name_reg = /h(i|ello), my name is (?<name>.*)/i #i means case insensitive
name_input = "Hi, my name is Zaphod Beeblebrox"
match_data = name_reg.match(name_input) #returns either a MatchData object or nil
match_data = name_input.match(name_reg) #works either way

if match_data.nil? #Always check for nil! Common error.
  puts "No match"
else
  match[0] #=> "Hi, my name is Zaphod Beeblebrox"
  match[1] #=> "i" #the first group, (i|ello)
  match[2] #=> "Zaphod Beeblebrox"
  #Because it was a named group, we can get it by name
  match[:name] #=> "Zaphod Beeblebrox"
  match["name"] #=> "Zaphod Beeblebrox"
  puts "Hello #{match[:name]}!"
end
```

匹配到的下标基于左侧括号顺序来计算的（整个正则表达式最先匹配到的下标即第一个组，小标为0）

```ruby
reg = /(((a)b)c)(d)/
match = reg.match 'abcd'
match[0] #=> "abcd"
match[1] #=> "abc"
match[2] #=> "ab"
match[3] #=> "a"
match[4] #=> "d"
```

## 28.4 节 量词

量词用来指定重复字符串的数量。

- **0 或者 1**

```ruby
/a?/
```

- **0 或者多个**

```ruby
/a*/
```

- **一个或者多个**

```ruby
/a+/
```

- **准确数量**

```ruby
/a{2,4}/ # 2个，三个或者4个
/a{2,}/ # 2个及以上
/a{,4}/ # 少于四个（包含0个）
```

默认，量词都是贪婪的，这意味着它们在进行匹配的同事尽可能使用多的字符来进行匹配。通常来说这一点不明显。

```ruby
/(?<site>.*) Stack Exchange/ =~ 'Motor Vehicle Maintenance & Repair Stack Exchange'
```

`site` 捕获组按照预想的设置捕获 `Motor Vehicle Maintenance & Repair`。但是，假设 `'Stack
Exchange'` 是字符串中可选的部分（因为它也可以是 `'Stack Overflow'`），所以这个不成熟的方案不会按照预期工作：

```ruby
/(?<site>.*)( Stack Exchange)?/
```

这个版本可以匹配，但捕获组包含 `'Stack Exchange'`，因为 `*` 是贪婪地，它吞掉了这个字符。解决方案是添加额外的 `?`，来使 `*` 变成惰性的。

```ruby
/(?<site>.*?)( Stack Exchange)?/
```

向任何量词增加 `?` 都能使其变成惰性的。

## 28.5 节 常见使用

正则表达式通常用在方法中当成参数，用来检测其他字符串是否存在，或者搜索或者替换其他字符串。

你经常看到以下代码：

```ruby
string = "My not so long string"
string[/so/] # gives so
string[/present/] # gives nil
string[/present/].nil? # gives true
```

因此，你可以简单使用以下代码，来检测字符串是否包含某个子串

```ruby
puts "found" if string[/so/]
```

更多高级但仍然简单和快：通过使用第二个参数，来搜索特定的组，在本例中 `2` 是第二个参数，因为编号是从1开始的，而不是0，组就是括号包起来的内容。

```ruby
string[/(n.t).+(l.ng)/, 2] # gives long
```

常用 `sub` 和 `gsub` 来做搜索和替换，`\1` 代表第一个组，`\2` 代表第二组：

```ruby
string.gsub(/(n.t).+(l.ng)/, '\1 very \2') # My not very long string
```

最后的结果也被缓存了，可以使用以下代码：

```ruby
$2 # gives long
```

## 28.6 节 `match ?`--布尔结果

返回 `true` 或者 `false`，这说明正则表达式匹配到了，或者没有匹配到，而不用更新 `$~` 和其他相关变量。假设第二个参数存在，它表示开始搜索的下标。

```ruby
/R.../.match?("Ruby") #=> true
/R.../.match?("Ruby", 1) #=> false
/P.../.match?("Ruby") #=> false
```

Ruby 2.4+

## 28.7 节 正则表达式的定义

在 Ruby 中正则表达式的创建有三种方式：

- 使用斜线 `//`
- 使用 `$r{}`
- 使用 `Regex.new`

```ruby
#The following forms are equivalent
regexp_slash = /hello/
regexp_bracket = %r{hello}
regexp_new = Regexp.new('hello')

string_to_match = "hello world!"

#All of these will return a truthy value
string_to_match =~ regexp_slash # => 0
string_to_match =~ regexp_bracket # => 0
string_to_match =~ regexp_new # => 0
```

## 28.8 节 字符类

描述了 symbol 范围（`range`）

显示枚举 symbol

```ruby
/[abc]/ # 'a' or 'b' or 'c'
```

或者使用 range

```ruby
/[a-z]/
```

也可以结合使用 range 和单个的 symbol

```ruby
/[a-cz]/ # 'a' or 'b' or 'c' or 'z'
```

`-` 也被当成字符

```ruby
/[-a-c]/ # '-' or 'a' or 'b' or 'c'
```

当前面加了 `^` 时，取反：

```ruby
/[^a-c]/ # Not 'a', 'b' or 'c'
```

以下是一些广泛使用的缩写和特定的字符：


| 符号/缩写 | 说明 |
| -- | -- |
| ^  |  Start of line|
| $  |  End of line|
| \A |  Start of string|
| \Z |  End of string, excluding any new line at the end of string|
| \z |  End of string|
| .  |  Any single character|
| \s |  Any whitespace character|
| \S |  Any non-whitespace character|
| \d |  Any digit|
| \D |  Any non-digit|
| \w |  Any word character (letter, number, underscore)|
| \W |  Any non-word character|
| \b |  Any word boundary|

`\n` 可以简单理解成换行。

保留字符需要使用进行转义，例如：`/` 或者 `[]` 及其他的保留使用反斜线

```ruby
\\ # => \
\[\] # => []
```