# 第 8 章 注释

## 8.1 节 单行注释及多行注释

注释在运行时被忽略， 是程序员可读的提示。目的是为了使源代码理解简单。

- 单行注释
```ruby
# 被用作单行注释
#!/usr/bin/ruby -w
# This is a single line comment.
puts "Hello World!"
```

执行时，上述代码只会输出  `Hello World!`

- 多行注释

多行注释通过添加 `=begin` 和 `=end` 语法(也就是注释块标记)， 如下

```ruby
#!/usr/bin/ruby -w
=begin
This is a multiline comment.
Write as many line as you want.
=end
puts "Hello World!"
```

执行时，上述代码只会输出  `Hello World!`