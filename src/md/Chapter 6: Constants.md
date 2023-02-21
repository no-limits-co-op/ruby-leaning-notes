# 第 6 章 常量

## 6.1 节 常量定义

```ruby
MY_CONSTANT = "Hello, world" # constant
Constant = 'This is also constant' # constant
my_variable = "Hello, venus" # not constant
```

常量以大写字母开头，在 Ruby 中任何以大写字母开头的都被认为是常量。因此 `class` 、 `module` 也是常量。所有字母都大写是最好的。

## 6.2 节 常量修改

```ruby
MY_CONSTANT = "Hello, world"
MY_CONSTANT = "Hullo, world"
```

上面代码运行会报警告， 假如要改变变量的值， 应当使用变量而非常量。 但一次修改一个字母是不报警告的，像这样：

```ruby
MY_CONSTANT = "Hello, world"
MY_CONSTANT[1] = "u"
```

现在，修改了 `MY_CONSTANT` 第二个字母之后， 变成了 `"Hullo, world"`

## 6.3 节 方法中不能定义常量

```ruby
def say_hi
  MESSAGE = "Hello"
  puts MESSAGE
end
```

上述代码会报错：`SyntaxError: (irb):2: dynamic constant assignment`

## 6.4 节 `class` 中常量的定义和修改

```ruby
class Message
  DEFAULT_MESSAGE = "Hello, world"

  def speak(message = nil)
    if message
      puts message
    else
      puts DEFAULT_MESSAGE
    end
  end
end
```

常量 `DEFAULT_MESSAGE` 的值能被接下来的代码改变

```ruby
Message::DEFAULT_MESSAGE = "Hullo, world"
```
