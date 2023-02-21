# 第 15 章 Symbols

## 15.1 节 创建 `symbol`

最常见创建Symbol对象的方式：在字符串识别码前面加一个冒号 `:`。

```ruby
:a_symbol # => :a_symbol
:a_symbol.class # => Symbol
```

以下是一些与字符串文字相结合定义 Symbol 的替代方法：

```ruby
:"a_symbol"
"a_symbol".to_sym
```

Symbol 还有一个 `%s` 序列，该序列支持任意分隔符，类似于 `%q` 和 `%Q` 对字符串的工作方式：

```ruby
%s(a_symbol)
%s{a_symbol}
```

`%s` 对于创建包含空格的 Symbol 特别有用：

```ruby
%s{a symbol} # => :"a symbol"
```

虽然可以使用某些字符串标识符创建一些有趣的符号（`:/`，`:[]`，`:^`，等等），但请注意，不能使用数字标识符创建符号：

```ruby
:1 # => syntax error, unexpected tINTEGER, ...
:0.3 # => syntax error, unexpected tFLOAT, ...
```

Symbol 可能以单个 `?` 或者 `!` 结尾或无需使用字符串文字作为符号的标识符：

```ruby
:hello? # :"hello?" is not necessary.
:world! # :"world!" is not necessary.
```

需要注意的是所有的不同方法，创建同一个 Symbol 时，他们相等

```ruby
:symbol.object_id == "symbol".to_sym.object_id
:symbol.object_id == %s{symbol}.object_id
```

Ruby 2.0之后，有一种从单词创建 Symbol 数组的快捷方式：

```ruby
%i(numerator denominator) == [:numerator, :denominator]
```

## 15.2 节 字符串转 Symbol

给出一个字符串：

```ruby
s = "something
```
有几种方式将字符串转成 `Symbol`

```ruby
s.to_sym
# => :something
:"#{s}"
# => :something
```

## 15.3 节 Symbol 转字符串

给出一个 Symbol

```ruby
s = :something
```

最简单的方式是通过 `Symbol#to_s` 方法进行转换：

```ruby
s.to_s
# => "something"
```

另一种方法是使用 `Symbol#id2name` 方法，它是 `Symbol#to_s` 方法的别名。但这是 Symbol 类独有的方法：

```ruby
s.id2name
# => "something"
```