# 第 20 章 Blocks and Procs and Lambdas

## 20.1 节 Lambdas

```ruby
# lambda using the arrow syntax
hello_world = -> { 'Hello World!' }
hello_world[]
# 'Hello World!'
# lambda using the arrow syntax accepting 1 argument
hello_world = ->(name) { "Hello #{name}!" }
hello_world['Sven']
# "Hello Sven!"
the_thing = lambda do |magic, ohai, dere|
  puts "magic! #{magic}"
  puts "ohai #{dere}"
  puts "#{ohai} means hello"
end
the_thing.call(1, 2, 3)
# magic! 1
# ohai 3
# 2 means hello
the_thing.call(1, 2)
# ArgumentError: wrong number of arguments (2 for 3)
the_thing[1, 2, 3, 4]
# ArgumentError: wrong number of arguments (4 for 3)
```

可以使用 `->` 创建，使用 `.()` 来调用 lambda：

```ruby
the_thing = ->(magic, ohai, dere) {
  puts "magic! #{magic}"
  puts "ohai #{dere}"
  puts "#{ohai} means hello"
}
the_thing.(1, 2, 3)
# => magic! 1
# => ohai 3
# => 2 means hello
```

在这里可以看到 lambda 几乎和 proc 一样。但是有些需要注意的地方：
- lambda 的参数数量是强制的，传入的参数数量不对，就会报 `ArgumentError`。它们都可以有默认参数，不定参数等。
- lambda 的返回值是从 lambda 中返回的，proc 的返回值是从区块之外返回的。

```ruby
def try_proc
  x = Proc.new {
    return # Return from try_proc
  }
  x.call
  puts "After x.call" # this line is never reached
end
def try_lambda
  y = -> {
    return # return from y
  }
  y.call
  puts "After y.call" # this line is not skipped
end
try_proc # No output
try_lambda # Outputs "After y.call"
```

## 20.2 节 Partial Application and Currying

技术上来说，Ruby 没有函数，只有方法。只是 Ruby 的方法和其他编程语言中的函数作用差不多。

```ruby
def double(n)
  n * 2
end
```

这个函数接收一个参数 n，返回 n 的两倍。我们现在定义一个更高倍数的方法（函数）：

```ruby
def triple(n)
  lambda { 3 * n }
end
```

`triple` 返回了一个函数，而不是一个数字。可以再 IRB 中测试：
```bash
$ irb --simple-prompt
>> def double(n)
>> n * 2
>> end
=> :double
>> def triple(n)
>> lambda {3 * n}
>> end
=> :triple
>> double(2)
=> 4
>> triple(2)
=> #<Proc:0x007fd07f07bdc0@(irb):7 (lambda)>
```
假如你想取值，你需要 `call` 或者 `reduce`：

```ruby
triple_two = triple(2)
triple_two.call # => 6
```

或者更简明地：

```ruby
triple_two(2).call
```

### Currying and Partial Applications

定义非常基础的方法不是特别又有用，但在定义不立即 `call` 或 `reduce` 的方法（或者函数）就非常有用了。例如：假如你想定义一个方法，一个数加另外一个特殊的数字（比如说 `add_one(2) = 3`）。如果定义非常基础的方法，你需要定义大量的方法，你才可以做到：

```ruby
def add_one(n)
  n + 1
end
def add_two(n)
  n + 2
end
```

但是，你也可以这样做：

```ruby
add = -> (a, b) { a + b }
add_one = add.curry.(1)
add_two = add.curry.(2)
```

使用 lambda 微分方式，我们可以认为 `add` 是 (`λa.(λb.(a+b))`)。Currying 是一种局部调用 `add` 的方式。因此 `add.curry.(1)` 是 `(λa.(λb.(a+b)))(1)`。它可以被缩减成这样 `(λb.(1+b))`。局部应用意味着我们先传一个参数给 `add`，剩下的一个参数稍后传入。输出就是特殊定义的函数。

### More useful examples of currying

假如我们有一个很长的公式，我们指定了具体参数。参考以下公式：

```
f(x, y, z) = sin(x\\*y)*sin(y\\*z)*sin(z\\*x)
```

上述公式是用在三维，但是假设我们只想要 `y` 和 `z`。或者说我们忽略 `x`，我们把它设置成 `pi/2`。让我们首先来制作通用公式：

```ruby
f = ->(x, y, z) {Math.sin(x*y) * Math.sin(y*z) * Math.sin(z*x)}
```

现在，让我们设置 `x` 来得到关于 `y` 和 `z` 的公式。

```ruby
f_yz = f.curry.(Math::PI/2)
```

然后 `f_yz` 通过 `call` 调用 lambda，即可：

```ruby
f_yz.call(some_value_y, some_value_z)
```

上面的非常简单，但假如我们想得倒关于 xz 的公式，我们怎么能把 y 设置成 `Math::PI/2` ？它就更复杂一点：

```ruby
f_xz = -> (x,z) {f.curry.(x, Math::PI/2, z)}
```

在本例中，我们需要为未被提前设置的参数的占位。出于一致性，我们就像这样写了：

```ruby
f_xy = -> (x,y) {f.curry.(x, y, Math::PI/2)}
```

以下 `f_yz` 是 lambda 微分的推导过程：

```
f = (λx.(λy.(λz.(sin(x*y) * sin(y*z) * sin(z*x))))
f_yz = (λx.(λy.(λz.(sin(x*y) * sin(y*z) * sin(z*x)))) (π/2) # Reduce =>
f_yz = (λy.(λz.(sin((π/2)*y) * sin(y*z) * sin(z*(π/2))))
```

再来看看 `f_xz`:

```ruby.rbs
f = (λx.(λy.(λz.(sin(x*y) * sin(y*z) * sin(z*x))))
f_xz = (λx.(λy.(λz.(sin(x*y) * sin(y*z) * sin(z*x)))) (λt.t) (π/2) # Reduce =>
f_xz = (λt.(λz.(sin(t*(π/2)) * sin((π/2)*z) * sin(z*t))))
```

更多 lambda 微分推导过程，参考这里。

## 20.3 节 对象作为块参数传入方法

在参数前面加一个 `&` 将会把参数作为代码块传给方法。对象将会通过 `to_proc` 方法转成一个 `Proc`。

```ruby
class Greeter
  def to_proc
    Proc.new do |item|
      puts "Hello, #{item}"
    end
  end
end
greet = Greeter.new
%w(world life).each(&greet)
```

这在 Ruby 中是一个非常常见的模式，并且很多标准类都提供了支持。

举例来说，Symbol 实现了 `to_proc` --把他们自己传入到参数中去：

```ruby
# Example implementation
class Symbol
  def to_proc
    Proc.new do |receiver|
      receiver.send self
    end
  end
end
```

这个特性就有了一个非常有用的习语：`&:symbol`，通常用在 Enumerable 对象中：

```ruby
letter_counts = %w(just some words).map(&:length) # [4, 4, 5]
```

## 20.4 节 转换成 Proc

对象通过 `&` 调用 `to_proc` 转换成 `proc`（`&` 允许他们自己当成代码块传入）。

Symbol 通过定义 `to_proc`，它会尝试调用对象上的相关方法来接收参数。

```ruby
p [ 'rabbit', 'grass' ].map( &:upcase ) # => ["RABBIT", "GRASS"]
```

方法上也定义了 `to_proc`。

```ruby
output = method( :p )
[ 'rabbit', 'grass' ].map( &output ) # => "rabbit\ngrass"
```

## 20.5 节 代码块

代码块是 `{}` 及其内部包裹的代码组成的（`{}` 通常用作单行代码块）或者 `do...end`（通常用作多行代码块）。

```ruby
5.times { puts "Hello world" } # recommended style for single line blocks
5.times do
  print "Hello "
  puts "world"
end # recommended style for multi-line blocks
5.times {
  print "hello "
  puts "world" } # does not throw an error but is not recommended
```

注意：`{}` 优先级比 `do...end` 高。

### Yileding

代码块，可以通过关键字 yield 应用在方法（函数）内部。

```ruby
def block_caller
  puts "some code"
  yield
  puts "other code"
end
block_caller { puts "My own block" } # the block is passed as an argument to the method.
#some code
#My own block
#other code
```

注意，假如 yield 调用的不是代码块，它会报错 `LocalJumpError`。出于这个考虑，Ruby 提供了另一个叫 `block_given?` 的方法，它允许你在调用 yield 之前检查是否存在代码块。

```ruby
def block_caller
  puts "some code"
  if block_given?
    yield
  else
    puts "default"
  end
  puts "other code"
end
block_caller
# some code
# default
# other code
block_caller { puts "not defaulted"}
# some code
# not defaulted
# other code
```

yield 可以向代码块中传入参数。

```ruby
def yield_n(n)
  p = yield n if block_given?
  p || n
end
yield_n(12) {|n| n + 7 }
#=> 19
yield_n(4)
#=> 4
```

这都是非常简单的例子。yield 在允许直接访问实例变量或者能在另一个对象内部计算时非常有用。例如：

```ruby
class Application
  def configuration
    @configuration ||= Configuration.new
    block_given? ? yield(@configuration) : @configuration
  end
end
class Configuration; end
app = Application.new
app.configuration do |config|
  puts config.class.name
end
# Configuration
#=> nil
app.configuration
#=> #<Configuration:0x2bf1d30>
```

在上述例子中，你可以看到 configuration 中使用 yield 的代码可读性比不停调用 `app.configuration.#method_name` 要好很多。你可以把所有的配置都包含在其中。

### Varibale

代码块中的参数都是代码块的局部变量（类似于函数参数），它们在代码块执行之后就消失了。

```ruby
my_variable = 8 3.times do |x|
  my_variable = x
  puts my_variable
end
puts my_variable
#=> 0
# 1
# 2
# 8
```

代码块是不能保存的，一旦被执行，就消失了。要保存代码块，你需要使用 proc 或者 lambda。