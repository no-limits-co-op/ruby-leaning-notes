# 第 4 章 变量作用域及访问性

## 4.1 节 类变量

类变量作用域为整个类， 可以在类中的任意位置声明，类中以 `@@` 开头的变量通常被认为是类变量

```ruby
class Dinosaur
  @@classification = "Like a Reptile, but like a bird"

  def self.classification
    @@classification
  end

  def classification
    @@classification
  end
end

dino = Dinosaur.new
dino.classification
# => "Like a Reptile, but like a bird"
Dinosaur.classification
# => "Like a Reptile, but like a bird"
```

类变量在相关类中共享， 可以被子类重写

```ruby
class TRex < Dinosaur
  @@classification = "Big teeth bird!"
end

TRex.classification
# => "Big teeth bird!"
Dinosaur.classification
# => "Big teeth bird!"
```

这种行为在大多数的情况下是不需要的，可以使用类实例变量来规避

module下的类变量不会在被引入的类中被重写

```ruby
module SomethingStrange
  @@classification = "Something Strange"
end

class DuckDinosaur < Dinosaur
  include SomethingStrange
end

DuckDinosaur.class_variables
# => [:@@classification]
SomethingStrange.class_variables
# => [:@@classification]
DuckDinosaur.classification
# => "Big teeth bird!"
```

## 4.2 节 局部变量

局部变量（与其他变量类不同）不需要任何前缀

```ruby
local_variable = "local"
p local_variable
# => local
```

它的作用域取决于定义它的位置，它不能在超出定义位置范围之外作用域使用。举个例子，假如局部变量定义在方法内部， 那么只能在方法内部使用。

```ruby
def some_method
  method_scope_var = "hi there"
  p method_scope_var
end

some_method
# hi there
# => hi there
method_scope_var
# NameError: undefined local variable or method `method_scope_var'
```
当然，局部变量不仅仅局限于方法，作为经验可以这样说，`do...end` 或者 `{}` 中声明的变量的作用域取决于定义变量的地方。

```ruby
2.times do |n|
  local_var = n + 1
  p local_var
end
# 1
# 2
# => 2
local_var
# NameError: undefined local variable or method `local_var'
```

尽管如此，如果变量是定义在 if 或者 case 代码块中，那么可在其父作用域中使用。

```ruby
if true
  usable = "yay"
end

p usable
# yay
# => "yay
```

尽管不能超出变量的定义范围之外使用， 但变量可以向其子级传递

```ruby
my_variable = "foo"
my_variable.split("").each_with_index do |char, i|
  puts "The character in string '#{my_variable}' at index #{i} is #{char}"
end
# The character in string 'foo' at index 0 is f
# The character in string 'foo' at index 1 is o
# The character in string 'foo' at index 2 is o
# => ["f", "o", "o"]
```

但不能向方法、类、模块中

```ruby
my_variable = "foo"
def some_method
  puts "you can't use the local variable in here, see? #{my_variable}"
end
some_method
# NameError: undefined local variable or method `my_variable'
```

用于块参数的变量（当然）是块本地的，但会掩盖之前定义的变量，而不覆盖它们。

```ruby
overshadowed = "sunlight"
["darkness"].each do |overshadowed|
  p overshadowed
end
# darkness
# => ["darkness"]
p overshadowed
# "sunlight"
# => "sunlight"
```

## 4.3 节 全局变量

全局变量具有全局范围，因此可以在任何地方使用。 它们的范围不取决于在被定义的位置。 当变量以 `$` 符号为前缀时，将被视为全局变量。

```ruby
$i_am_global = "omg"
class Dinosaur
  def instance_method
    p "global vars can be used everywhere. See? #{$i_am_global}, #{$another_global_var}"
  end

  def self.class_method
    $another_global_var = "srsly?"
    p "global vars can be used everywhere. See? #{$i_am_global}"
  end
end

Dinosaur.class_method
# "global vars can be used everywhere. See? omg"
# => "global vars can be used everywhere. See? omg"
dinosaur = Dinosaur.new
dinosaur.instance_method
# "global vars can be used everywhere. See? omg, srsly?"
# => "global vars can be used everywhere. See? omg, srsly?"
```

由于全局变量可以在任何地方定义并且在任何地方都可见，因此调用 `undefined` 全局变量将返 `nil` 而不是引发错误。

```ruby
p $undefined_var
# nil
# => nil
```

尽管全局变量易于使用，但强烈建议不要使用，除非它是常量。

## 4.4 节 实例变量

实例变量具有对象范围，它们可以在对象中的任何位置声明，实例是类级别声明的变量，只会在类对象中可见。 以 `@` 为前缀时的变量将被视为实例变量。 实例变量用于设置和获取对象属性，如果没有被定义将返回 `nil`。

```ruby
class Dinosaur
  @base_sound = "rawrr"

  def initialize(sound = nil)
    @sound = sound || self.class.base_sound
  end

  def speak
    @sound
  end

  def try_to_speak
    @base_sound
  end

  def count_and_store_sound_length
    @sound.chars.each_with_index do |char, i|
      @sound_length = i + 1
      p "#{char}: #{sound_length}"
    end
  end

  def sound_length
    @sound_length
  end

  def self.base_sound
    @base_sound
  end
end

dino_1 = Dinosaur.new
dino_2 = Dinosaur.new "grrr"
Dinosaur.base_sound
# => "rawrr"
dino_2.speak
# => "grrr"
```

示例变量定义在类级别，但在对象级别是访问不到的

```ruby
dino_1.try_to_speak
# => nil
```

但是，当没有 sound 传递给 new 方法时，我们使用实例变量 @base_sound 来实例化  sound ：

```ruby
dino_1.speak
# => "rawwr"
```

实例变量可以在对象内部任意位置被定义，甚至是在代码块中

```ruby
dino_1.count_and_store_sound_length
# "r: 1"
# "a: 2"
# "w: 3"
# "r: 4"
# "r: 5"
# => ["r", "a", "w", "r", "r"]
dino_1.sound_length
# => 5
```

实例变量在实例对象之间是相互独立的。

```ruby
dino_2.sound_length
# => nil
```

因为类也是 Ruby 中的对象，这可用于创建不会被子类覆盖的类级别变量。

```ruby
class DuckDuckDinosaur < Dinosaur
  @base_sound = "quack quack"
end

duck_dino = DuckDuckDinosaur.new
duck_dino.speak
# => "quack quack"
DuckDuckDinosaur.base_sound
# => "quack quack"
Dinosaur.base_sound
# => "rawrr"
```