# 第 26 章 继承

## 26.1 节 子类

继承允许类基于存在的类定义特殊的行为

```ruby
class Animal
  def say_hello
    'Meep!'
  end

  def eat
    'Yumm!'
  end
end

class Dog < Animal
  def say_hello
    'Woof!'
  end
end

spot = Dog.new
spot.say_hello # 'Woof!'
spot.eat # 'Yumm!'
```

在本例中：

- `Dog` 继承了 `Animal`，它是子类。
- `Dog` 从 `Animal` 得到了两个方法 `say_hello` 和 `eat`
- `Dog` 重写了 `say_hello`，实现了不同的功能


## 26.2 节 继承了什么？

### 方法是继承的

```ruby
class A
  def boo; p 'boo' end
end

class B < A; end

b = B.new
b.boo # => 'boo'
```

### 类方法是继承的

```ruby
class A
   def self.boo; p 'boo' end
end

class B < A; end
p B.boo # => 'boo'
```

### 常量是继承的

```ruby
class A
  WOO = 1
end

class B < A; end
p B::WOO # => 1
```

### 实例变量是继承的

```ruby
class A
  attr_accessor :ho
  def initialize
    @ho = 'haha'
  end
end

class B < A; end
b = B.new
p b.ho # => 'haha'
```

需要注意的是，如果你重写了方法，但是在初始化实例变量的时候，没有调用 `super` 方法，它们的值就会是 `nil`。继续上述例子：

```ruby
class C < A
  def initialize; end
end

c = C.new
p c.ho # => nil
```

### 类实例变量不会被继承

```ruby
class A
  @foo = 'foo'

  class << self
    attr_accessor :foo
  end
end

class B < A; end
p B.foo # => nil
# The accessor is inherited, since it is a class method
#
B.foo = 'fob' # possible
```

### 类变量不是真正的继承

它们在基类和所有的子类中是同一个变量。

```ruby
class A
  @@foo = 0

  def initialize
    @@foo += 1
    p @@foo
  end
end

class B < A;end

a = A.new # => 1
b = B.new # => 2
```

继续上述例子：

```ruby
class C < A
  def initialize
    @@foo = -10
    p @@foo
  end
end

a = C.new # => -10
b = B.new # => -9
```


## 26.3 节 多继承

多继承是允许一个类继承多个类（多个父类）的一个特性。Ruby 不支持多继承，它只支持单继承（一个类只能有一个父类），但你可以使用模块来组合出更复杂的类。

## 26.4 节 Mixins

Mixins 是一个漂亮的方式来实现某些类似多继承的东西。它允许我们继承，更确切地说是通过 `include` 一个模块中的方法到一个类中。这些方法可以被 `include` 成实例方法或者类方法。以下例子描绘了这个设计。

```ruby
module SampleModule
  def self.included(base)
    base.extend ClassMethods
  end

  module ClassMethods
    def method_static
      puts "This is a static method"
    end
  end

  def insta_method
    puts "This is an instance method"
  end
end

class SampleClass
  include SampleModule
end

sc = SampleClass.new
sc.insta_method
prints "This is an instance method"

sc.class.method_static
prints "This is a static method"
```

## 26.5 节 通过继承重构已经存在的类

假设，我们有两个类，`Dog` 和 `Cat`。

```ruby
class Cat
  def eat
    die unless has_food?
    self.food_amount -= 1
    self.hungry = false
  end

  def sound
    puts "Meow"
  end
end

class Dog
  def eat
    die unless has_food?
    self.food_amount -= 1
    self.hungry = false
  end

  def sound
    puts "Woof"
  end
end
```

`eat` 方法在两个类中是完全一样的。尽管它能正常运行，但是它不好维护。更多动物有同样的 `eat` 方法时，这个问题变得更糟糕。继承可以解决这个问题。

```ruby
class Animal
  def eat
    die unless has_food?
    self.food_amount -= 1
    self.hungry = false
  end
  # No sound method
end

class Cat < Animal
  def sound
    puts "Meow"
  end
end

class Dog < Animal
  def sound
    puts "Woof"
  end
end
```

我们已经新增加了一个类 `Animal`，并且把 `eat` 方法移入到这个类。之后我们让 `Cat` 和 `Dog` 继承了这个新的公共超类。然后移除重复代码。
