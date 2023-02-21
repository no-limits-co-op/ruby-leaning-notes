# 第 9 章 数组

## 9.1 节 字符串数组

创建数组，可以使用 ruby 的 pecent string 语法

```ruby
array = %w( one, two, three, four )
```

在功能上等同于以下代码：

```ruby
array = ['one', 'two', 'three', 'four']
```

除了 `%w()` ，也可以使用其他的匹配分隔符：`%w{...}`，`%w[...]`，`%w<...>`

也可以使用任意的非字母分隔符，例如：`%w!...!`， `%w#...#` 或者 `%w@...@`

`%W` 替代 `%w` 来进行合并插值字符串， 考虑以下情形：

```ruby
var  = 'hello'

%w(#{var}) # => ["\#{var}"]
%W(#{var}) # => ["hello"]
```

通过空格加斜线进行多单词插入

```ruby
%w(Colorado California New \ York) # => ["Colorado", "California", "New York"]
```

## 9.2 节 通过 `Array::new` 创建字符串

使用 Array 的类方法 `Array::new` 创建空数组：

```ruby
Array.new
```

传入数字，设置数组长度：

```ruby
Array.new 3 # => [nil,nil,nil]
```

有两种方式传入设置默认值

- 第二个参数传入指定值
- 传入一个代码块，使用下标生成值

```ruby
Array.new 3, :x #=> [:x, :x, :x]
Array.new 3, { |i| i.to_s } #=> ["0", "1", "2"
a = Array.new 3, "X" # Not recommended.
a[1].replace "C" # a => ["C", "C", "C"]
b = Array.new(3) { "X" } # The recommended way.
b[1].replace "C" # b => ["X", "C", "X"]]
```

## 9.3 节 创建 `Symbol` 数组

2.0 以上版本

```ruby
array = %i(one, two, three, four)
```

上述代码创建了数组 `[:one, :two, :three, :four]`

除了 `%i(...)`， 也可以使用 `${....}`、 `$[....]`、 `$!....!`

如果想用插值， 需使用 `%I`

2.0 及以上版本

```ruby
a = 'hello'
b = 'goodbye'
array_one = %I(#{a} #{b} world)
array_two = %i(#{a} #{b} world)
```

上述代码创建了数组：`array_one = [:hello, :goodbye, :world]` 和 `array_two = [:"\#{a}", :"\#{b}", :world]`

## 9.4 节  操作数组元素

增加元素

```ruby
[1, 2, 3] << 4
# => [1, 2, 3, 4]
[1, 2, 3].push(4)
# => [1, 2, 3, 4]
[1, 2, 3].unshift(4)
# => [4, 1, 2, 3]
[1, 2, 3] << [4, 5]
# => [1, 2, 3, [4, 5]]
```

移除元素

```ruby
array = [1, 2, 3, 4]
array.pop
# => 4
array
# => [1, 2, 3]
array = [1, 2, 3, 4]
array.shift
# => 1
array
# => [2, 3, 4]
array = [1, 2, 3, 4]
array.delete(1)
# => 1
array
# => [2, 3, 4]
array = [1,2,3,4,5,6]
array.delete_at(2) # delete from index 2
# => 3
array
# => [1,2,4,5,6]
array = [1, 2, 2, 2, 3]
array - [2]
# => [1, 3] # removed all the 2s
array - [2, 3, 4]
# => [1] # the 4 did nothing
```

组合数组

```ruby
[1, 2, 3] + [4, 5, 6]
# => [1, 2, 3, 4, 5, 6]
[1, 2, 3].concat([4, 5, 6])
# => [1, 2, 3, 4, 5, 6]
[1, 2, 3, 4, 5, 6] - [2, 3]
# => [1, 4, 5, 6]
[1, 2, 3] | [2, 3, 4]
# => [1, 2, 3, 4]
[1, 2, 3] & [3, 4]
# => [3]
```

也可以数组相乘，例如

```ruby
[1, 2, 3] * 2
# => [1, 2, 3, 1, 2, 3]
```

## 9.5 节 访问元素

通过索引访问数组的元素。 数组索引编号从 0 开始。

```ruby
%w(a b c)[0] # => 'a'
%w(a b c)[1] # => 'b'
```

使用 范围（range） 裁剪数组

```ruby
%w(a b c d)[1..2] # => ['b', 'c'] (indices from 1 to 2, including the 2)
%w(a b c d)[1...2] # => ['b'] (indices from 1 to 2, excluding the 2)
```

这将返回一个新的数组， 不会影响到原来的数组， Ruby 也支持负值索引

```ruby
%w(a b c)[-1] # => 'c'
%w(a b c)[-2] # => 'b'
```

可以结合使用负值加位置索引

```ruby
%w(a b c d e)[1...-1] # => ['b', 'c', 'd']
```

其他有用的方法
使用 `first` 访问数组的第一个元素

```ruby
[1, 2, 3, 4].first # => 1
```

或者使用 `first(n)` 访问数组的前 n 元素，并以数组形式返回。

```ruby
[1, 2, 3, 4].first(2) # => [1, 2]
```

同样的有 `last`、`last(n)`

```ruby
[1, 2, 3, 4].last # => 4
[1, 2, 3, 4].last(2) # => [3, 4]
```

使用 `sample` 随机访问数组元素

```ruby
[1, 2, 3, 4].sample # => 3
[1, 2, 3, 4].sample # => 1
```

或者使用 `sample(n)`:

```ruby
[1, 2, 3, 4].sample(2) # => [2, 1]
[1, 2, 3, 4].sample(2) # => [3, 4]
```

## 9.6 节 使用字面量构造方法创建数组

可以通过将元素列表括在方括号（`[` 和 `]`）中来创建数组。 此表示法中的数组元素用逗号分隔：

```ruby
array = [1, 2, 3, 4]
```

数组可以包含任意组合的任何类型的对象，对类型没有限制：

```ruby
array = [1, 'b', nil, [3, 4]]
```

## 9.7 节 分解

任何数组都可以通过将其元素分配给多个变量来快速分解。 一个简单的例子：

```ruby
arr = [1, 2, 3]
# ---
a = arr[0]
b = arr[1]
c = arr[2]
# --- or, the same
a, b, c = arr
```

使用 splat 运算符 ( `*` ) 在变量前面放入一个包含其他变量未捕获的所有元素的数组。 如果没有剩余，则分配空数组。 在单次赋值中只能使用一个 splat ：

```ruby
a, *b = arr # a = 1; b = [2, 3]
a, *b, c = arr # a = 1; b = [2]; c = 3
a, b, c, *d = arr # a = 1; b = 2; c = 3; d = []
a, *b, *c = arr # SyntaxError: unexpected *
```

分解是安全的，永远不会引发错误。 `nil` 分配在没有足够元素的地方，匹配 `[]` 运算符在访问越界索引时的行为：

```ruby
arr[9000] # => nil
a, b, c, d = arr # a = 1; b = 2; c = 3; d = nil
```

分解尝试在被分配的对象上隐式调用 `to_ary`。 通过在您的类型中实现此方法，您可以获得分解它的能力

```ruby
class Foo
  def to_ary
    [1, 2]
  end
end
a, b = Foo.new # a = 1; b = 2
```

假如被分解的对象没有响应 ？to_ary ，它被视为一个单元素数组：

```ruby
1.respond_to?(:to_ary) # => false
a, b = 1 # a = 1; b = nil
```

分解也可以通过使用 () 分隔的分解表达式来嵌套，而不是使用单个元素：

```ruby
arr = [1, [2, 3, 4], 5, 6]
a, (b, *c), *d = arr # a = 1; b = 2; c = [3, 4]; d = [5, 6]
```

这实际上与 splat 正好相反。

实际上，任何分解表达式都可以用 `()` 分隔。 但是对于第一级分解是可选的。

```ruby
a, b = [1, 2] (a, b) = [1, 2] # the same thing
```

边缘情况：单个标识符不能用作解构模式，无论是外部的还是嵌套的：

```ruby
(a) = [1] # SyntaxError
a, (b) = [1, [2]] # SyntaxError
```

将数组文字分配给解构表达式时，可以省略外部 `[]`：

```ruby
a, b = [1, 2]
a, b = 1, 2 # exactly the same
```

这称为并行分配，但它在底层使用相同的分解。 这对于在不使用额外临时变量的情况下交换变量值特别方便：

```ruby
t = a; a = b; b = t # an obvious way
a, b = b, a # an idiomatic way
(a, b) = [b, a] # ...and how it works
```

在构建赋值的右侧时会捕获值，因此使用与源和目标相同的变量相对安全

## 9.8 节 数组并集、交集和差集

```ruby
x = [5, 5, 1, 3]
y = [5, 2, 4, 3]
```

并集 (`|`) 包含两个数组中的元素，并删除了重复项：

```ruby
x | y
#=> [5, 1, 3, 2, 4]
```

交集 (`&`) 包含存在于第一个和第二个数组中的元素：

```ruby
x & y
#=> [5,3]
```

差集 (`-`) 包含存在于第一个数组中且不存在于第二个数组中的元素：

```ruby
x - y
#=> [1]
```

## 9.9 节 使用 `compact` 移除数组中的 `nil` 元素

如果一个数组恰好有一个或多个 `nil` 元素并且需要删除这些元素，则 `Array#compact` 或 `Array#compact!` 可以使用方法，如下:

```ruby
array = [ 1, nil, 'hello', nil, '5', 33]
array.compact # => [ 1, 'hello', '5', 33]
#notice that the method returns a new copy of the array with nil removed,
#without affecting the original
array = [ 1, nil, 'hello', nil, '5', 33]
#If you need the original array modified, you can either reassign it
array = array.compact # => [ 1, 'hello', '5', 33]
array = [ 1, 'hello', '5', 33]
#Or you can use the much more elegant 'bang' version of the method
array = [ 1, nil, 'hello', nil, '5', 33]
array.compact # => [ 1, 'hello', '5', 33]
array = [ 1, 'hello', '5', 33]
```

最后，请注意如果 `#compact` 或 `#compact!` 在没有 `nil` 元素的数组上调用，这些将返回 `nil`。

```ruby
array = [ 'foo', 4, 'life']
array.compact # => nil
array.compact! # => nil
```

## 9.10 节 获取数组的所有组合/排列

当使用块调用 `permutation` 方法时，会产生一个二维数组，该数组由数字集合的所有有序序列组成。

如果在没有块的情况下调用此方法，它将返回一个枚举器。 要转换为数组，请调用 `to_a` 方法。

| 例子 | 结果 |
| --- | --- |
| `[1,2,3].permutation` |           `Enumerator: [1,2,3]:permutation` |
| `[1,2,3].permutation.to_a` |     `[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]` |
| `[1,2,3].permutation(2).to_a` |   `[[1,2],[1,3],[2,1],[2,3],[3,1],[3,2]]` |
| `[1,2,3].permutation(4).to_a` |  `[] -> No permutations of length 4` |

另一方面，组合方法在使用块调用时会产生一个二维数组，该数组由数字集合的所有序列组成。 与排列不同，顺序在组合中被忽略。 例如，
[1,2,3] 与 [3,2,1] 相同

| 例子 | 结果 |
| --- | --- |
| `[1,2,3].combination(1)`        | `Enumerator: [1,2,3]:combination` |
| `[1,2,3].combination(1).to_a` |  `[[1],[2],[3]]` |
| `[1,2,3].combination(3).to_a` |  `[[1,2,3]]` |
| `[1,2,3].combination(4).to_a` |  `[] -> No combinations of length 4` |

单独调用组合方法会产生一个枚举器。 要获取数组，请调用 `to_a` 方法。

重复组合和重复排列方法是相似的，除了相同的元素可以重复多次。

例如，序列 [1,1]、[1,3,3,1]、[3,3,3] 在常规组合和排列中无效。

```ruby
[1,2,3].combination(3).to_a.length            # 1
[1,2,3].repeated_combination(3).to_a.length   # 6
[1,2,3,4,5].combination(5).to_a.length        # 1
[1,2,3].repeated_combination(5).to_a.length   # 126
```

## 9.11 节 `Inject` 和 `Reduce`

Inject 和 Reduce 是同一事物的不同名称。 在其他语言中，这些函数通常称为折叠（如 foldl 或 foldr）。 这些方法可用于每个 `Enumerable` 对象。Inject 采用两个参数函数并将其应用于数组中的所有元素对。
对于数组 [1, 2, 3]，可以通过指定起始值将所有这些与起始值零相加, 代码如下：

```ruby
[1,2,3].reduce(0) {|a,b| a + b} # => 6
```

在这里，我们向函数传递了一个起始值和一个将所有值相加的块。 该块首先以 0 作为 a 和 1 作为 b 运行，然后将其结果作为下一个 a，因此我们将 1 添加到第二个值 2。然后我们将 3 的结果添加到列表中的最后一个元素 (也是 3) 中，从而得到我们的结果 (6)。

如果我们省略第一个参数，它会将 a 设置为列表中的第一个元素，因此上面的示例与以下示例相同：

```ruby
[1,2,3].reduce {|a,b| a + b} # => 6
```

此外，我们可以将命名函数作为符号传递，而不是传递带有函数的块，可以带起始值，也可以不带起始值。 有了这个，上面的例子可以写成这样：

```ruby
[1,2,3].reduce(0, :+) # => 6
```

或省略初始值

```ruby
[1,2,3].reduce(:+) # => 6
```

## 9.12 节  数组过滤

通常我们只想对满足特定条件的数组元素进行操作：

- `Select`

将返回符合特定条件的元素

```ruby
array = [1, 2, 3, 4, 5, 6]
array.select { |number| number > 3 } # => [4, 5, 6]
```

- `Reject`

将返回不符合特定条件的元素

```ruby
array = [1, 2, 3, 4, 5, 6]
array.reject { |number| number > 3 } # => [1, 2, 3]
```

`#select` 和 `#reject` 都返回一个数组，因此可以将它们链接起来：

```ruby
array = [1, 2, 3, 4, 5, 6]
array.select { |number| number > 3 }.reject { |number| number < 5 } # => [5, 6]
```

## 9.13 节  `#map`

`#map`，由 `Enumerable` 提供，通过在每个元素上调用一个块并收集结果来创建一个数组：

```ruby
[1, 2, 3].map { |i| i * 3 }
# => [3, 6, 9]
['1', '2', '3', '4', '5'].map { |i| i.to_i }
# => [1, 2, 3, 4, 5]
```

原始数组没有被修改； 返回一个新数组，其中包含转换后的值，其顺序与源值相同。 如果要修改原始数组，可以使用 `map!`。
在 `map` 方法中，您可以对数组中的所有元素调用方法或使用 `proc`

```ruby
# call to_i method on all elements
%w(1 2 3 4 5 6 7 8 9 10).map(&:to_i)
# => [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
# using proc (lambda) on all elements
%w(1 2 3 4 5 6 7 8 9 10).map(&->(i){ i.to_i * 2})
# => [2, 4, 6, 8, 10, 12, 14, 16, 18, 20]
```

`map` 是 `collect` 的同义词。

## 9.14 节 数组和 splat (`*`) 运算符

`*` 运算符可用于解包变量和数组，以便它们可以作为单独的参数传递给方法。

如果还没有，这可以用于将单个对象包装在 Array 中：

```ruby
def wrap_in_array(value)
  [*value]
end
wrap_in_array(1)
#> [1]
wrap_in_array([1, 2, 3])
#> [1, 2, 3]
wrap_in_array(nil)
#> []
```

在上面的示例中， `wrap_in_array` 方法接受一个参数 value。

如果 value 是一个数组，它的元素被解包并创建一个包含这些元素的新数组。

如果 value 是单个对象，则创建一个包含该单个对象的新数组。

如果 value 为 `nil`，则返回一个空数组

splat 运算符在某些情况下用作方法中的参数时特别方便。 例如，它允许以一致的方式处理 `nil`、单个值和数组：

```ruby
def list(*values)
  values.each do |value|
    # do something with value
    puts value
  end
end
list(100)
#> 100
list([100, 200])
#> 100
#> 200
list(nil)
# nothing is outputted
```

## 9.15 节 二维数组

使用 `Array::new` 构造函数，您可以初始化具有给定大小的数组，并在其每个插槽中初始化一个新数组。
内部数组也可以被赋予大小和初始值。

例如，要创建一个 3x4 的零数组：

```ruby
array = Array.new(3) { Array.new(4) { 0 } }
```

上面生成的数组使用 `p` 打印时如下所示：

```ruby
[[0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0]]
```

可以像这样读取或写入元素：

```ruby
x = array[0][1]
array[2][3] = 2
```

## 9.16 节 将多维数组转换为一维（扁平）数组

```ruby
[1, 2, [[3, 4], [5]], 6].flatten # => [1, 2, 3, 4, 5, 6]
```

如果有一个多维数需要将它变成一个简单的（即一维）数组，你可以使用 `#flatten` 方法。

## 9.17 节 获取不重复的数组元素

如果您需要读取避免重复的数组元素，请使用 `#uniq` 方法：

```ruby
a = [1, 1, 2, 3, 4, 4, 5]
a.uniq
#=> [1, 2, 3, 4, 5]
```

相反，如果您想从数组中删除所有重复的元素，您可以使用 `#uniq!` 方法：

```ruby
a = [1, 1, 2, 3, 4, 4, 5]
a.uniq!
#=> [1, 2, 3, 4, 5]
```

虽然输出相同，但 `#uniq!` 还存储新数组：

```ruby
a = [1, 1, 2, 3, 4, 4, 5]
a.uniq
#=> [1, 2, 3, 4, 5]
a
#=> [1, 1, 2, 3, 4, 4, 5]
a = [1, 1, 2, 3, 4, 4, 5]
a.uniq!
#=> [1, 2, 3, 4, 5]
a
#=> [1, 2, 3, 4, 5]
```

## 9.18 节 创建数字数组

创建数字数组的常规方法：

```ruby
numbers = [1, 2, 3, 4, 5]
```

`Range` 对象可广泛用于创建数字数组：

```ruby
numbers = Array(1..10) # => [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
numbers = (1..10).to_a # => [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

#step 和 #map 方法允许我们对数字范围施加条件：

odd_numbers = (1..10).step(2).to_a # => [1, 3, 5, 7, 9]
even_numbers = 2.step(10, 2).to_a # => [2, 4, 6, 8, 10]
squared_numbers = (1..10).map { |number| number * number } # => [1, 4, 9, 16, 25, 36, 49, 64, 81,
100]
```

上述所有方法都急切地加载数字。 如果您必须懒加载它们：

```ruby
number_generator = (1..100).lazy # => #<Enumerator::Lazy: 1..100>
number_generator.first(10) # => [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

## 9.19 节 创建一个连续数字数组或字母

这可以通过在 `Range 对象上调用 `Enumerable#to_a` 轻松完成：

```ruby
(1..10).to_a #=> [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

`(a..b)` 表示它将包括 a 和 b 之间的所有数字。 要排除最后一个数字，请使用 `a...b`

```ruby
a_range = 1...5
a_range.to_a #=> [1, 2, 3, 4]
```

或者

```ruby
('a'..'f').to_a #=> ["a", "b", "c", "d", "e", "f"]
('a'...'f').to_a #=> ["a", "b", "c", "d", "e"]
```

创建数组的便捷快捷方式是 `[*a..b]`

```ruby
[*1..10] #=> [1,2,3,4,5,6,7,8,9,10]
[*'a'..'f'] #=> ["a", "b", "c", "d", "e", "f"]
```

## 9.20 节 任何对象转换为数组

要从任何对象获取 Array，请使用 `Kernel#Array`。

下面是一个例子：

```ruby
Array('something') #=> ["something"]
Array([2, 1, 5]) #=> [2, 1, 5]
Array(1) #=> [1]
Array(2..4) #=> [2, 3, 4]
Array([]) #=> []
Array(nil) #=> []
```

例如，可以从以下代码替换 `join_as_string` 方法

```ruby
def join_as_string(arg)
  if arg.instance_of?(Array)
    arg.join(',')
  elsif arg.instance_of?(Range)
    arg.to_a.join(',')
  else
    arg.to_s
  end
end
join_as_string('something') #=> "something"
join_as_string([2, 1, 5]) #=> "2,1,5"
join_as_string(1) #=> "1"
join_as_string(2..4) #=> "2,3,4"
join_as_string([]) #=> ""
join_as_string(nil) #=> ""
```

到下面的代码

```ruby
def join_as_string(arg)
  Array(arg).join(',')
end
```
