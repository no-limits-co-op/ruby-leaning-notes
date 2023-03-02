# 第 25 章 Classes

## 25.1 节 构造方法

一个类只能有一个构造方法，方法名叫做 `initialize`。当创建类的实例时，自动调用该方法。

```ruby
class Customer
  def initialize(name)
    @name = name.capitalize
  end
end

sarah = Customer.new('sarah')
sarah.name #=> 'Sarah'
```

## 25.2 节 类的创建

你使用 `class` 关键字来定义类。

```ruby
class MyClass
end
```

一旦定义了类，你可以使用 `.new` 方法来创建类的实例。

```ruby
somevar = MyClass.new
# => #<MyClass:0x007fe2b8aa4a18>
```

## 25.3 节 访问级别（Access Level）

Ruby 有三个访问级别。他们是 `public`、`private` 和 `protected`。

紧跟在关键后面的方法，就是该访问级别的方法。假如方法前面没有定义访问级别，隐式的定义成 `public` 级别的方法。

### `public` 方法

`public` 方法应该描述被创建对象的行为，这些方法可以在被创建对象作用域外调用。

```ruby
class Cat
  def initialize(name)
    @name = name
  end

  def speak
    puts "I'm #{@name} and I'm 2 years old"
  end
 ...
end

new_cat = Cat.new("garfield")
#=> <Cat:0x2321868 @name="garfield">
new_cat.speak
#=> I'm garfield and I'm 2 years
```

这些方法都是 `public` 方法，他们描述了初始化 cat 的行为（`initialize` 方法的行为），以及 `speak` 方法的行为.

`public` 关键不是必须的，但可以用来规避 `private` 和 `protected`。

```ruby
def MyClass
  def first_public_method

  end

  private

  def private_method
  end

  public

  def second_public_method
  end
end
```

### `private` 方法

`private` 方法再对象外面不可访问。他们在对象内部使用。使用 cat 的例子来说明：

```ruby
class Cat
  def initialize(name)
    @name = name
  end

  def speak
    age = calculate_cat_age # here we call the private method
    puts "I'm #{@name} and I'm #{age} years old"
  end

  private

  def calculate_cat_age
    2 * 3 - 4
  end
end
my_cat = Cat.new("Bilbo")
my_cat.speak #=> I'm Bilbo and I'm 2 years old
my_cat.calculate_cat_age #=> NoMethodError: private method `calculate_cat_age' called for
#<Cat:0x2321868 @name="Bilbo">
```

正如你所看到的上述例子，新创建地 Cat 对象在内部才能访问 `calculate_cat_age` 方法。我们将计算结果赋值给变量 `age`，最后我们将 name 和 age 都打印出来。

当我们尝试在对象外面调用 `calculate_cat_age` 方法时，我们接收到了报错 `NoMethodError`，因为它是 `private` 级别的方法，懂了吗？

### `protected` 方法

`protected` 方法和 `private` 方法非常像，它们不能在对象之外被访问。但是，使用 `self` 方法，在相同类型的对象的上下文中，`protected` 方法可以被访问。

```ruby
class Cat
  def initialize(name, age)
    @name = name
    @age = age
  end

  def speak
    puts "I'm #{@name} and I'm #{@age} years old"
  end
  # this == method allows us to compare two objects own ages.
  # if both Cat's have the same age they will be considered equal.
  def ==(other)
    self.own_age == other.own_age
  end

  protected

  def own_age
    self.age
  end
end
cat1 = Cat.new("ricky", 2)
=> #<Cat:0x007fe2b8aa4a18 @name="ricky", @age=2>
cat2 = Cat.new("lucy", 4)
=> #<Cat:0x008gfb7aa6v67 @name="lucy", @age=4>
cat3 = Cat.new("felix", 2)
=> #<Cat:0x009frbaa8V76 @name="felix", @age=2>
```

可以看出来，cat 对象加了一个 `age` 属性，用 name 和 age 创建三个 cat 对象。我们接下来调用 `protected` 级别的 ` own_age` 方法来比较 cat 对象的 age 属性。

```ruby
cat1 == cat2
=> false
cat1 == cat3
=> true
```

上方的例子，我们是可以使用 `self.own_age` 方法取回 cat1 的 age 属性，来传入和 cat2 的 age 进行对比（cat2 的age 属性取值过程和 cat1 一致）。


## 25.4 节 类方法类型

类有三种方法类型：实例方法，单例方法和类方法。

### 实例方法

这些方法可以被类的实例调用。

```ruby
class Thing
  def somemethod
    puts "something"
  end
end

foo = Thing.new # create an instance of the class
foo.somemethod # => something
```

### 类方法

它们是静态方法，例如，它们可以被类调用，而不用在类的实例上。

```ruby
class Thing
  def Thing.hello(name)
    puts "Hello, #{name}!"
  end
end
```

使用 `self` 和类名是等价的。以下代码和以上代码是等价的。

```ruby
class Thing
  def self.hello(name)
    puts "Hello, #{name}!"
  end
end
```

调用示例：

```ruby
Thing.hello("John Doe") # prints: "Hello, John Doe!"
```

### 单例方法

它们只对特定实例有用，但不是所有。

```ruby
# create an empty class
class Thing
end
# two instances of the class
thing1 = Thing.new
thing2 = Thing.new
# create a singleton method
def thing1.makestuff
  puts "I belong to thing one"
end

thing1.makestuff # => prints: I belong to thing one
thing2.makestuff # NoMethodError: undefined method `makestuff' for #<Thing>
```

单例方法和类方法都被称为自类（eigenclasses）。基本上，ruby 所做的就是创建一个匿名类，来保存这些方法，这样就不会干扰创建实例。

另一个方式是使用 `class << constructor`。例如：

```ruby
# a class method (same as the above example)
class Thing
  class << self # the anonymous class
    def hello(name)
      puts "Hello, #{name}!"
    end
  end
end
Thing.hello("sarah") # => Hello, sarah!

# singleton method
class Thing
end

thing1 = Thing.new
class << thing1
 def makestuff
  puts "I belong to thing one"
 end
end
thing1.makestuff # => prints: "I belong to thing one"
```


## 25.5 节 实例变量的getter和setter

我们有三个方法：
1. `attr_reader`：允许在类的外部读变量
2. `attr_writer`：允许在类的外部写变量
3. `attr_accessor`：允许在类的外部读写变量

```ruby
class Cat
  attr_reader :age # you can read the age but you can never change it
  attr_writer :name # you can change name but you are not allowed to read
  attr_accessor :breed # you can both change the breed and read it

  def initialize(name, breed)
     @name = name
     @breed = breed
     @age = 2
  end

  def speak
     puts "I'm #{@name} and I am a #{@breed} cat"
  end
end

my_cat = Cat.new("Banjo", "birman")
# reading values:
my_cat.age #=> 2
my_cat.breed #=> "birman"
my_cat.name #=> Error
# changing values
my_cat.age = 3 #=> Error
my_cat.breed = "sphynx"
my_cat.name = "Bilbo"
my_cat.speak #=> I'm Bilbo and I am a sphynx cat
```

注意，参数都是 symbols，它通过创建一个方法来正常工作。

```ruby
class Cat
  attr_accessor :breed
end
```

它的代码和以下代码类似：

```ruby
class Cat
  def breed
    @breed
  end

  def breed= value
    @breed = value
  end
end
```

## 25.6 节 `new`, `allocate` 以及 `initialize`

在许多编程语言中，类实例的创建是通过使用特殊关键字 `new` 进行创建的。在 Ruby 中 `new` 关键字也用来创建类的实例，但它不是一个关键字，他是一个静态（类）方法，和其它的静态（类）方法没有任何区别，大概是这样定义的：

```ruby
class MyClass
  def self.new(*args)
     obj = allocate
     obj.initialize(*args) # oversimplied; initialize is actually private
     obj
  end
end
```

`allocate` 方法才是创建一个没有实例化类的真正魔法。

注意返回值不是 `initialize` 的值，`obj` 才是返回值。这样让你瞬间明白，为什么你可以在 `initialize` 方法不用担心返回值，它最终会返回 `self`。

上述是正常的 `new` 方法，对所有的类都能正常工作，我们也可以按需重新定义 `new`，或者定义不同工作方式。例如：

```ruby
class MyClass
  def self.extraNew(*args)
    obj = allocate
    obj.pre_initialize(:foo)
    obj.initialize(*args)
    obj.post_initialize(:bar)
    obj
  end
end
```

## 25.7 节 类的动态创建

通过使用 `Class.new` 来动态创建类。

```ruby
# create a new class dynamically
MyClass = Class.new
# instantiate an object of type MyClass
my_class = MyClass.new
```

在上面的例子中，创建了一个新类，并且赋值给了 `MyClass`。它可以像其他类一样被实例化。

`Class.new` 接收一个 `Class`， 它会成为动态创建类的超类。

```ruby
# dynamically create a class that subclasses another
Staffy = Class.new(Dog)
# instantiate an object of type Staffy
lucky = Staffy.new
lucky.is_a?(Staffy) # true
lucky.is_a?(Dog) # true
```

`Class.new` 也可以接收一个代码块，代码块的内容是新创建的类。这允许定义方法。

```ruby
Duck =
  Class.new do
     def quack
        'Quack!!'
     end
   end
# instantiate an object of type Duck
duck = Duck.new
duck.quack # 'Quack!!'
```

## 25.8 节 类和实例变量

类有许多特殊的变量类型，更容易的用来共享数据。

实例变量，变量名前加 `@`。在不同方法中使用同一个变量，它非常好用。

```ruby
class Person
  def initialize(name, age)
    my_age = age # local variable, will be destroyed at end of constructor
    @name = name # instance variable, is only destroyed when the object is
  end

  def some_method
    puts "My name is #{@name}." # we can use @name with no problem
  end

  def another_method
    puts "My age is #{my_age}." # this will not work!
  end
end

mhmd = Person.new("Mark", 23)
mhmd.some_method #=> My name is Mark.
mhmd.another_method #=> throws an error
```

类变量，变量名前加 `@@`。共享在一个类的所有实例之间。

```ruby
class Person
  @@persons_created = 0 # class variable, available to all objects of this class

  def initialize(name)
    @name = name
    # modification of class variable persists across all objects of this class
    @@persons_created += 1
  end

  def how_many_persons
     puts "persons created so far: #{@@persons_created}"
  end
end

mark = Person.new("Mark")
mark.how_many_persons #=> persons created so far: 1
helen = Person.new("Helen")

mark.how_many_persons #=> persons created so far: 2
helen.how_many_persons #=> persons creat
# you could either ask mark or helen
```

全局变量，变量名前加 `$`。它们在程序中的任何地方都可以访问，所以一定要明智的使用它们。

```ruby
$total_animals = 0

class Cat
  def initialize
    $total_animals += 1
  end
end

class Dog
  def initialize
    $total_animals += 1
  end
end

bob = Cat.new()
puts $total_animals #=> 1
fred = Dog.new()
puts $total_animals #=> 2
```
