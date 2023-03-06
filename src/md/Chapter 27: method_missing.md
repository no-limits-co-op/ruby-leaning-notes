# 第 27 章 method_missing

|参数| 详情|
| -- | --|
| 方法|被调用的方法名（在本例中是 `:say_moo`，记住它是 symbol）|
| *args | 被调用方法传入的参数，可以是任何类型，或者没有 |
| &block | 传入被调用方法的代码块， 它是 `do` 代码块，或者是 `{}` 代码块 |

## 27.1 节 捕获调用未定义的方法

```ruby
class Animal
  def method_missing(method, *args, &block)
    "Cannot call #{method} on Animal"
  end
end

=> Animal.new.say_moo
> "Cannot call say_moo on Animal"
```

## 27.2 节 使用代码块

```ruby
class Animal
  def method_missing(method, *args, &block)
    if method.to_s == 'say'
      block.call
    else
      super
    end
  end
end

=> Animal.new.say{ 'moo' }
=> "moo"
```

## 27.3 节 使用参数

```ruby
class Animal
  def method_missing(method, *args, &block)
    say, speak = method.to_s.split("_")

    if say == "say" && speak
      return speak.upcase if args.first == "shout"
      speak
    else
      super
    end
  end
end

=> Animal.new.say_moo
=> "moo"
=> Animal.new.say_moo("shout")
=> "MOO"
```

## 27.4 节 使用缺失的方法

```ruby
class Animal
  def method_missing(method, *args, &block)
    say, speak = method.to_s.split("_")

    if say == "say"
      speak
    else
      super
    end
  end
end

=> a = Animal.new
=> a.say_moo
=> "moo"
=> a.shout_moo
=> NoMethodError: undefined method `shout_moo'
```
