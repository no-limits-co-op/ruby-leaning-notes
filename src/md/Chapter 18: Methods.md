# 第 18 章 方法

Ruby 中的函数提供了有组织的、可复用的代码来预制一组动作。函数简化了编码过程，防止冗余逻辑，使代码更容易理解。本章介绍了 Ruby 中函数的定义、使用，参数、yield 语句以及作用域。

## 18.1 节 方法定义

方法用 `def` 关键字定义，紧接着是方法名称和括号中的可选参数列表。在 def 和 end 中间的 Ruby 代码是方法体。

```ruby
def hello(name)
 "Hello, #{name}"
end
```

调用方法指定方法名称，调用的对象有时候称之为接收方，以及指定给已经命名的0个或者多个参数。

```ruby
hello("world")
# => 'Hello, World'
```

如果没有接收方不明确，就是 self。

参数名称可以再方法体中档变量使用，参数值来自方法调用处传入的值。

```ruby
hello("World")
# => "Hello, World"
hello("All")
# => "Hello, All"
```

## 18.2 节 yield 传入代码块

你可以想你的方法中传入一个代码块，且它可以被调用多次。可以通过发送程序或者 lambda 等实现，但用 yield 实现更简单更快。

```ruby
def simple(arg1,arg2)
 puts "First we are here: #{arg1}"
 yield
 puts "Finally we are here: #{arg2}"
 yield
end
simple('start','end') { puts "Now we are inside the yield" }
#> First we are here: start
#> Now we are inside the yield
#> Finally we are here: end
#> Now we are inside the yield
```

注意 `{ puts ... }` 不在括号里面，它在后面，这也意味着我们只能有一个 yield 代码块，我们可以将参数传到 yield 里面去。

```ruby
def simple(arg)
  puts "Before yield"
  yield(arg)
  puts "After yield"
end
simple('Dave') { |name| puts "My name is #{name}" }
#> Before yield
#> My name is Dave
#> After yield
```

通过 yield，我们可以更简单的进行迭代（传入函数进行迭代）。

```ruby
def countdown(num)
  num.times do |i|
   yield(num-i)
 end
end

countdown(5) { |i| puts "Call number #{i}" }
#> Call number 5
#> Call number 4
#> Call number 3
#> Call number 2
#> Call number 1
```

实际上，foreach，each 和 times 也是在类中通过 yield 实现的。

假如，你想确定是否传入了代码块，使用 `block_given?`：

```ruby
class Employees
  def names
    ret = []
    @employees.each do |emp|
      if block_given?
       yield(emp.name)
     else
       ret.push(emp.name)
     end
    end
    ret
  end
end
```

上述例子，假设 Employees 类中有一个 `@employees` 列表，它使用 `each` 迭代列表，它通过 `name` 方法获取员工名称。假设我们传入了代码块，我们就将 `name` 传入 `yield` 中去，否则我们就将其将入到返回的数组中去。

## 18.3 节 默认参数

```ruby
def make_animal_sound(sound = 'Cuack')
 puts sound
end

make_animal_sound('Mooo') # Mooo
make_animal_sound # Cuack
```

多个参数也可以有默认值。

```ruby
def make_animal_sound(sound = 'Cuack', volume = 11)
 play_sound(sound, volume)
end

make_animal_sound('Mooo') # Spinal Tap cow
```

但是，不支持第一个参数就不支持第二个参数，预期用位置参数，不如用关键参数。

```ruby
def make_animal_sound(sound: 'Cuack', volume: 11)
 play_sound(sound, volume) // 此方法不知道在哪定义的
end

make_animal_sound(volume: 1) # Duck whisper
```

或者通过 hash 参数来接收。

```ruby
def make_animal_sound(options = {})
 options[:sound] ||= 'Cuak'
 options[:volume] ||= 11
 play_sound(sound, volume)
end
make_animal_sound(:sound => 'Mooo')
```

参数的默认值，可以是任何 ruby 表达式。表达式将会在方法的上下文中运行。因此你甚至可以定义一个局部变量。但代码审查是不会通过的，礼貌的指出了这一点。

```ruby
def make_animal_sound( sound = ( raise 'TUU-too-TUU-too...' ) ); p sound; end
make_animal_sound 'blaaaa' # => 'blaaaa'
make_animal_sound # => TUU-too-TUU-too... (RuntimeError)
```

## 18.4 节 可选参数（`*` 号运算符）

```ruby
def welcome_guests(*guests)
 guests.each { |guest| puts "Welcome #{guest}!" }
end

welcome_guests('Tom') # Welcome Tom!

welcome_guests('Rob', 'Sally', 'Lucas')
# Welcome Rob!
# Welcome Sally!
# Welcome Lucas!
```

注意，`welcome_guests(['Rob', 'Sally', 'Lucas'])` 将会输出 `Welcome ['Rob', 'Sally', 'Lucas']`，假如你有一个列表，你可以这样做：`welcome_guests(*['Rob', 'Sally', 'Lucas'])`，这样就和 `welcome_guests('Rob', 'Sally', 'Lucas')` 得到一样的结果。

## 18.5 节 参数默认值和可选参数混合使用

```ruby
def my_mix(name,valid=true, *opt)
 puts name
 puts valid
 puts opt
end

my_mix('me')
# 'me'
# true
# []
my_mix('me', false)
# 'me'
# false
# []
my_mix('me', true, 5, 7)
# 'me'
# true
# [5,7]
```

## 18.6 节 函数当代码块使用

在 ruby 中许多函数接收代码块参数，例如：

```ruby
[0, 1, 2].map {|i| i + 1}
=> [1, 2, 3]
```

假如你已经写好了一个实现你想要功能的函数，可以通过&method(:fn)来将其转换成代码块。

```ruby
def inc(num)
 num + 1
end
[0, 1, 2].map &method(:inc)
=> [1, 2, 3]
```

## 18.7 节 单个参数

```ruby
def say_hello_to(name)
 puts "Hello #{name}"
end

say_hello_to('Charles') # Hello Charles
```

## 18.8 节 元组参数

函数可以接收数组参数，并立即将其解构赋值给局部变量。参见 Mathias Meyer's blog。

```ruby
def feed( amount, (animal, food) )
  p "#{amount} #{animal}s chew some #{food}"
end

feed 3, [ 'rabbit', 'grass' ] # => "3 rabbits chew some grass"
```

## 18.9 节 捕获未定义的关键字参数（双星号）

`**` 的工作方式和 `*` 类似，但是它使用于关键字参数。

```ruby
def options(required_key:, optional_key: nil, **other_options)
  other_options
end
options(required_key: 'Done!', foo: 'Foo!', bar: 'Bar!')
#> { :foo => "Foo!", :bar => "Bar!" }
```

上述例子中，假如不使用 `**`，就会报一个错误：`ArgumentError: unknown keyword: foo, bar`。

```ruby
def without_double_splat(required_key:, optional_key: nil)
  # do nothing
end
without_double_splat(required_key: 'Done!', foo: 'Foo!', bar: 'Bar!')
#> ArgumentError: unknown keywords: foo, bar
```

当你有一堆你想传入的变量时，并且不想过滤参数名时，非常方便。

```ruby
def options(required_key:, optional_key: nil, **other_options)
  other_options
end
my_hash = { required_key: true, foo: 'Foo!', bar: 'Bar!' }
options(my_hash)
#> { :foo => "Foo!", :bar => "Bar!" }
```

也可以是用 `**` 运算符解构 hash，这允许我们直接向方法中传入其他的 hash 值。

```ruby
my_hash = { foo: 'Foo!', bar: 'Bar!' }
options(required_key: true, **my_hash)
#> { :foo => "Foo!", :bar => "Bar!" }
```

## 18.10 节 多个必须参数

```ruby
def greet(greeting, name)
  puts "#{greeting} #{name}"
end
greet('Hi', 'Sophie') # Hi Sophie
```

## 18.11 节 方法定义是表达式

方法定义在 ruby 2.x 版本中返回了表示方法名的标识。

```ruby
class Example
  puts def hello
 end
end

#=> :hello
```

这允许使用非常有趣的元编程技术。例如，方法可以被另一个方法包裹：

```ruby
class Class
  def logged(name)
    original_method = instance_method(name)
    define_method(name) do |*args|
     puts "Calling #{name} with #{args.inspect}."
     original_method.bind(self).call(*args)
     puts "Completed #{name}."
   end
  end
end

class Meal
  def initialize
    @food = []
  end
  logged def add(item)
    @food << item
  end
end
meal = Meal.new
meal.add "Coffee"
# Calling add with ["Coffee"].
# Completed add.
```
