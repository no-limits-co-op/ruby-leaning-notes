# 第 16 章 Comparable模块

- Params	Details
- other	要与 self 进行比较的实例
- Comparable 模块是 Ruby 中最受欢迎的模块，它的目的是提供便利的比较方法。
使用它，必须 `include Comparable` 和定义 `<=>` 方法

```ruby
class Rectangle
 include Comparable
 def initialize(a, b)
   @a = a
   @b = b
 end

 def area
   @a * @b
 end

 def <=>(other)
   area <=> other.area
 end
end

r1 = Rectangle.new(1, 1)
r2 = Rectangle.new(2, 2)
r3 = Rectangle.new(3, 3)
r2 >= r1 # => true
r2.between? r1, r3 # => true
r3.between? r1, r2 # => false
```
