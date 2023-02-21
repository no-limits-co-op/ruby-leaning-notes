# 第 7 章 Ruby 中特殊的常量

## 7.1 节 `__FILE__`

是从当前执行目录到文件的相对路径

假设我们有这个目录结构：*/home/stackoverflow/script.rb*

**script.rb** 包含

```ruby
puts __FILE__
```

假如在 */home/stackoverflow* 目录下执行 ruby 脚本 **script.rb**， 那么  `__FILE__` 输出 `script.rb`。假如在 */home* 目录下执行 ruby 脚本 **script.rb**， 那么 `__FILE__` 输出 `stackoverflow/script.rb`

在不存在 `__dir__` 的 2.0 版本之前，获取脚本的路径非常实用。

```ruby
Note __FILE__ 不是 __dir__
```

## 7.2 节 __dir__

`__dir__` 不是常量，而是一个方法
`__dir__` 等同 `File.dirname(File.realpath(__FILE__))`

## 7.3 节 `$PROGRAM_NAME` or `$0`

包含执行脚本的名称

假如执行的话，等同于 `__FILE__`

## 7.4 节 `$$`

运行此脚本的 Ruby 的进程号

## 7.5 节 `$1`, `$2`等

包含上一个成功匹配的模式中相应括号集中的子模式，而不是计算已经退出的嵌套块中匹配的模式，如果最后一个模式匹配失败，则为 `nil`。

这些变量都是只读的。

## 7.6 节 `ARGV` or `$*`

为脚本提供的命令行参数。 Ruby 解释器的选项已被删除。

## 7.7 节 `STDIN`

标准输入，默认值为 `$stdin`

## 7.8 节 `STDOUT`

标准输出，默认值为 `$stdout`

## 7.9节 `STDERR`

标准错误输出， 默认值为 `$stderr`

## 7.10 节 `$stderr`

当前标准错误输出

## 7.11 节 `$stdout`

当前标准输出

## 7.12 节 `$stdin`

当前标准错误输入

## 7.13 节 `ENV`

包含当前环境变量的类哈希对象。设置 ENV 的值会改变子进程的运行环境
