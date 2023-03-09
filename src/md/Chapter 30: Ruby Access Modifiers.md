# 第 30 章 Ruby 的访问修饰器

访问控制的各种方法、数据成员、初始化方法。

## 30.1 节 实例变量和类变量

首先让我们梳理一下什么事实例变量：它们更像是对象的属性，它们在对象创建的时候就会被初始化。实例变量可以通过实例方法访问，每一个对象都有每一个对象的实例变量，对象之间不共享实例变量。

`Sequence` 类有实例变量 `@form`、`@to` 和 `@by`。

```ruby
class Sequence
  include Enumerable

  def initialize(from, to, by)
    @from = from
    @to = to
    @by = by
  end

  def each
    x = @from
    while x < @to
      yield x
      x = x + @by
    end
  end

  def *(factor)
    Sequence.new(@from*factor, @to*factor, @by*factor)
  end

  def +(offset)
    Sequence.new(@from+offset, @to+offset, @by+offset)
  end
end

object = Sequence.new(1,10,2)
object.each do |x|
  puts x
end

=begin
Output:
1
3
5
7
9
=end

object1 = Sequence.new(1,10,3)
object1.each do |x|
  puts x
end

=begin
Output:
1
4
7
=end
```

**类变量** 把类变量当成和 Java 中的静态变量，它们可以在不同对象之间共享。类变量存储在堆中。

```ruby
class Sequence
  include Enumerable

  @@count = 0

  def initialize(from, to, by)
    @from = from
    @to = to
    @by = by
    @@count = @@count + 1
  end

  def each
    x = @from
    while x < @to
      yield x
      x = x + @by
    end
  end

  def *(factor)
    Sequence.new(@from*factor, @to*factor, @by*factor)
  end

  def +(offset)
    Sequence.new(@from+offset, @to+offset, @by+offset)
  end

  def getCount
    @@count
  end
end

object = Sequence.new(1,10,2)
object.each do |x|
  puts x
end

=begin
Output:
1
3
5
7
9
=end

object1 = Sequence.new(1,10,3)
object1.each do |x|
  puts x
end
=begin
Output:
1
4
7
=end

puts object1.getCount
# Output: 2
```

`object` 和 `object1` 共享了 `@@count` 的值。

### **Ruby 的类变量和实例变量与 Java 的对比：**

```java
Class Sequence{
  int from, to, by;

  Sequence(from, to, by){// constructor method of Java is equivalent to initialize method of ruby
    this.from = from;// this.from of java is equivalent to @from indicating currentObject.from
    this.to = to;
    this.by = by;
  }

  public void each(){
    int x = this.from;//objects attributes are accessible in the context of the object.
    while x > this.to
      x = x + this.by
  }
}
```

## 30.2 访问控制

**Java 的访问控制与 Ruby 的访问控制对比：**假设 Java 中的方法被定义成 `private`，它只能被同类中的其他方法访问（调用）。假如方法呗定义成 `protected`，它可以被同包中其他类访问，以及不同包中此类的子类访问。当方法被定义成 `public`，它可以被所有类访问。在 Java 中，访问控制的可见性取决于类在继承/包中的位置。

**在 Ruby 中，继承结构层次/模块并不适用。它完全取决于哪一个对象是方法的接收器**

**对于 Ruby 的 `private` 方法**，它永远不能使用显示接收者来调用它。我们只能用隐式接收者来调用 `private` 方法。

这也意味着，我们在声明类中调用 `private` 方法，还有在类的子类中调用 `private` 方法。

```ruby
class Test1
  def main_method
    method_private
  end

  private

  def method_private
    puts "Inside methodPrivate for #{self.class}"
  end
end

class Test2 < Test1
  def main_method
    method_private
  end
end

Test1.new.main_method
Test2.new.main_method
# Inside methodPrivate for Test1
# Inside methodPrivate for Test2

class Test3 < Test1
  def main_method
    self.method_private #We were trying to call a private method with an explicit receiver and if called in the same class with self would fail.
  end
end

Test1.new.main_method
# This will throw NoMethodError
```

你不能从定义在类的结构层次之外，调用 `private` 方法。

**`protected` 方法**可以被隐式接收者调用，和 `private` 方法类似。除此之外，如果接收者是 `self` 或者是同类的对象，`protected` 方法也可以被显示接收者调用。

```ruby
class Test1
  def main_method
    method_protected
  end

  protected

  def method_protected
    puts "InSide method_protected for #{self.class}"
  end
end

class Test2 < Test1
  def main_method
    method_protected # called by implicit receiver
  end
end

class Test3 < Test1
  def main_method
    self.method_protected # called by explicit receiver "an object of the same class"
  end
end

# InSide method_protected for Test1
# InSide method_protected for Test2
# InSide method_protected for Test3

class Test4 < Test1
  def main_method
    Test2.new.method_protected # "Test2.new is the same type of object as self"
  end
end

Test4.new.main_method

class Test5
  def main_method
    Test2.new.method_protected
  end
end

Test5.new.main_method

# This would fail as object Test5 is not subclass of Test1
```

**思考一下 `public` 方法具有最大可见性**

**总结**

1. `public`：`public` 方法具有最大可见性
2. `protected`：`protected` 方法可以被隐式接收者调用，和 `private` 方法类似。除此之外，如果接收者是 `self` 或者是同类的对象，`protected` 方法也可以被显示接收者调用
3. `private`：`private` 方法它永远不能使用显示接收者来调用它。我们只能用隐式接收者来调用 `private` 方法。这也意味着，我们在声明类中调用 `private` 方法，还有在类的子类中调用 `private` 方法。
