# 第 21 章 迭代

## 21.1 节 Each

Ruby 有许多类型的枚举器，但是最基础的最简单的类型就是 each。我们将打印出 1-10 以内的数，是奇数或者偶数。

通常有两种方式传入被调用的代码块。代码块是代码片段，传入到方法中，并在方法中执行。`each` 方法接收一个代码块，它在每一个迭代的时候，都被调用。

代码块传入方法有两种方式：
方式一： 单行模式

```ruby
(1..10).each { |i| puts i.even? ? 'even' : 'odd' }
```

这是非常简略的 Ruby 处理方式。接下来，我们分解说明：
1. `(1..10)` 是是一个包10在内的 Range。假如我们不想包含 10 在内，我们这样写(1...10)。
2. `.each` 是一个枚举器，它枚举它在作用的对象上每一个元素。在本例中，它作用于 Range 中的每一个数字。
3. `|i| puts i.even? ? 'even' : 'odd'` 是 `each` 语句接收的代码块。他可以被进一步分解：
  a. `|i|` 它指的是每一个在 Range 中的元素，在代码块的内部用变量i表示。
  b. `puts` 是 Ruby 输出方法，它输出之后会自动换行（如果我们自动换行，我们可以使用 `print`）
  c. `i.even?` 判断数字是不是偶数，我们也可以用 `i % 2 == 0`，但我们倾向于使用内置方法。
  d. `? 'even' : 'odd'` 是 Ruby 三目运算符。这种方式的三目运算符表示：`expression ？ a : b`。它是 `if expression a else b end` 形式的简写形式。

对于代码多于一行的代码块，应该使用多行代码块传入。
方式二：多行模式

```ruby
(1..10).each do |i|
  if i.even?
    puts 'even'
  else
    puts 'odd'
  end
end
```

在多行代码中，`do` 代替了单行模式风格中的开始的 `{`, `end` 代替了结束的 `}`。

Ruby 也支持 `reverse_each`，它从后往前进行遍历。

```ruby
@arr = [1,2,3,4]
puts @arr.inspect # output is [1,2,3,4]

print "Reversed array elements["
@arr.reverse_each do |val|
  print " #{val} " # output is 4 3 2 1
end
print "]\n
```

## 21.2 节 在类中的实现
Enumerable 是 Ruby 中最受欢迎的模块。它的目的是向我们提供迭代方法，诸如 `map`、`select`、`reduce` 等等。包括 Array、Hash、Range 都使用了 Enumerable。要使用它，就需要 `include Enumerable` 和实现 `each`。

```ruby
class NaturalNumbers
  include Enumerable

  def initialize(upper_limit)
    @upper_limit = upper_limit
  end

  def each(&block)
    0.upto(@upper_limit).each(&block)
  end
end

n = NaturalNumbers.new(6)
n.reduce(:+) # => 21
n.select(&:even?) # => [0, 2, 4, 6]
n.map { |number| number ** 2 } # => [0, 1, 4, 9, 16, 25, 36]
```

## 21.3 节 遍历复杂对象

**Arrays**

你可以遍历嵌套数组：

```ruby
[[1, 2], [3, 4]].each { |(a, b)| p "a: #{ a }", "b: #{ b }" }
```

下面的语法也是可以的：

```ruby
[[1, 2], [3, 4]].each { |a, b| "a: #{ a }", "b: #{ b }" }
```

输出结果为：

```ruby
"a: 1"
"b: 2"
"a: 3"
"b: 4"
```

**Hashes**

你可以遍历键值对：

```ruby
{a: 1, b: 2, c: 3}.each { |pair| p "pair: #{ pair }" }
```

输出结果为：

```ruby
"pair: [:a, 1]"
"pair: [:b, 2]"
"pair: [:c, 3]"
```

你可以同时遍历 key 和 value：

```ruby
{a: 1, b: 2, c: 3}.each { |(k, v)| p "k: #{ k }", "v: #{ k }" }
```

输出结果为：

```ruby
"k: a"
"v: a"
"k: b"
"v: b"
"k: c"
"v: c"
```

## 21.4 节 for 迭代器

以下代码遍历 4 到 13（包含）

```ruby
for i in 4...13
  puts "this is #{i}.th number"
end
```

我们也可以遍历数组：

```ruby
names = ['Siva', 'Charan', 'Naresh', 'Manish']
for name in names
  puts name
end
```

## 21.5 节 带下标的迭代器
有时，在遍历的时候，我们需要知道正在遍历元素的下标（index）。基于这个需求，Ruby 提供了 `with_index` 方法，它可以作用于所有的遍历器。通常是把 `with_index` 加在遍历器后面，你就可以遍历了，`index` 当成第二个参数传入到代码块。

```ruby
[2,3,4].map.with_index { |e, i| puts "Element of array number #{i} => #{e}" }
#Element of array number 0 => 2
#Element of array number 1 => 3
#Element of array number 2 => 4
#=> [nil, nil, nil]
```

`with_index` 有一个可选参数，第一个下标的默认是 0：

```ruby
[2,3,4].map.with_index(1) { |e, i| puts "Element of array number #{i} => #{e}" }
#Element of array number 1 => 2
#Element of array number 2 => 3
#Element of array number 3 => 4
#=> [nil, nil, nil]
```

有一个特殊的方法 `each_with_index`，它和 `each.with_index` 的区别在于--不能传递参数，所以第一个下标一直是 0：

```ruby
[2,3,4].each_with_index { |e, i| puts "Element of array number #{i} => #{e}" }
#Element of array number 0 => 2
#Element of array number 1 => 3
#Element of array number 2 => 4
```

## 21.6 节 Map
返回修改之后的值，原对象的值不变。举个例子：

```ruby
arr = [1, 2, 3]
arr.map { |i| i + 1 } # => [2, 3, 4]
arr # => [1, 2, 3]
```

`map!` 改变原对象的值：

```ruby
arr = [1, 2, 3]
arr.map! { |i| i + 1 } # => [2, 3, 4]
arr # => [2, 3, 4]
```

注意：你也可以使用 `collect` 来处理。