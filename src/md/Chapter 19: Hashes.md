# 第 19 章 哈希

hash 是类似字典的集合，它包含不可重复的key及key对应的值。也被称为关联数组，他们类似于数组，不同之处在于，数组使用整数当下标，hash允许你使用任何对象类型。通过应用hash中的项来检索或者创建条目。

## 19.1 节 创建哈希

ruby 中的哈希是一个实现了哈希表的对象，映射 key 和 value。ruby 支持使用字面量语法 `{}` 定义 hash：

```ruby
my_hash = {} # an empty hash
grades = { 'Mark' => 15, 'Jimmy' => 10, 'Jack' => 10 }
```

哈希也可以通过标准的 `new` 方法创建：

```ruby
my_hash = Hash.new # any empty hash
my_hash = {} # any empty hash
```

哈希 key 对应的值可以是任何类型的，包裹复杂类型，例如数组，对象，和其他的 hash：

```ruby
mapping = { 'Mark' => 15, 'Jimmy' => [3,4], 'Nika' => {'a' => 3, 'b' => 5} }
mapping['Mark'] # => 15
mapping['Jimmy'] # => [3, 4]
mapping['Nika'] # => {"a"=>3, "b"=>5}
```

key 也可以是任何类型的，包括复杂类型：

```ruby
mapping = { 'Mark' => 15, 5 => 10, [1, 2] => 9 }
mapping['Mark'] # => 15
mapping[[1, 2]] # => 9
```

标识符通常用作 hash 的 key，在 ruby 1.9 版本中引入了一个新的语法来简化它。以下 hash 值的定义是等价的：

```ruby
# Valid on all Ruby versions
grades = { :Mark => 15, :Jimmy => 10, :Jack => 10 }
# Valid in Ruby version 1.9+
grades = { Mark: 15, Jimmy: 10, Jack: 10 }
```

以下哈希（在所有版本中都是有效的）和标识符作为 key 的 hash 是不一样的，因为它们的 key 都是字符串：

```ruby
grades = { "Mark" => 15, "Jimmy" => 10, "Jack" => 10 }
```

两个版本的语法可以混合使用，但不推荐这做。

```ruby
mapping = { :length => 45, width: 10 }
```

在 ruby 2.2+ 版本，有一个可供选择的语法，来创建 symbol作为key的哈希（特别是key包含空格）：

```ruby
grades = { "Jimmy Choo": 10, :"Jack Sparrow": 10 }
# => { :"Jimmy Choo" => 10, :"Jack Sparrow" => 10}
```

## 19.2 节 设置默认值
默认，查找一个不存在的 key 的值将返回 `nil`。你也可以指定值，如果查找 key 不存在的值，将返回设置好的这个值。这被称之为引用默认值，当然这个值不仅仅是一个简单类型的数据，还可以是一个复杂类型的数据。

hash 的默认值，可以传给器构造器：

```ruby
h = Hash.new(0)
h[:hi] = 1
puts h[:hi] # => 1
puts h[:bye] # => 0 returns default value instead of nil
```

默认值也可以设置在构建过的 hash 上：

```ruby
my_hash = { human: 2, animal: 1 }
my_hash.default = 0
my_hash[:plant] # => 0
```

重点注意，默认值不是每次获取时就会复制一份，这将导致默认值是引用类型时，会有一些意料之外的结果。

```ruby
# Use an empty array as the default value
authors = Hash.new([])
# Append a book title, homer is not existing, so we will get the default value []
authors[:homer] << 'The Odyssey'
# All new keys map to a reference to the same array:
authors[:plato] # => ['The Odyssey]
```

围绕这个问题，Hash 的构造器接收一个代码块，key 访问一次，代码块就会执行一次，所以也可以实现默认值。

```ruby
authors = Hash.new { [] }
# Note that we're using += instead of <<, see below
authors[:homer] += ['The Odyssey']
authors[:plato] # => []
authors # => {:homer=>["The Odyssey"]}
```

注意，我们使用了 `+=`，而不是 `<<`，因为默认值不会自动复制给 hash；使用 `<<` 会添加到数组上，但 `authors[:homer]` 将保持不变，仍然是没有定义。

```ruby
authors[:homer] << 'The Odyssey' # ['The Odyssey']
authors[:homer] # => []
authors # => {}
```

为了每次都能访问到默认值， 同时计算复杂默认值，设置默认值传入的代码块可以同时传入 hash 和 key：

```ruby
authors = Hash.new { |hash, key| hash[key] = [] }

authors[:homer] << 'The Odyssey'
authors[:plato] # => []
authors # => {:homer=>["The Odyssey"], :plato=>[]}
```

你也可以使用默认值来对数据做一些处理，例如返回的值，依赖于 key 或者其他数据：

```ruby
chars = Hash.new { |hash,key| key.length }

chars[:test] # => 4
```

你甚至可以创建一个更复杂的 hash 值；

```ruby
page_views = Hash.new { |hash, key| hash[key] = { count: 0, url: key } }

page_views["http://example.com"][:count] += 1

page_views # => {"http://example.com"=>{:count=>1, :url=>"http://example.com"}}
```

通过 default_proc 来为已经存在的 hash 设置默认值处理代码块：

```ruby
authors = {}
authors.default_proc = proc { [] }
authors[:homer] += ['The Odyssey']
authors[:plato] # => []
authors # {:homer=>["The Odyssey"]}
```

## 19.3 节 值的访问

单个的 hash 值可以通过 `[]` 和 `[]=` 方法来进行读写：

```ruby
my_hash = { length: 4, width: 5 }

my_hash[:length] #=> => 4

my_hash[:height] = 9

my_hash #=> {:length => 4, :width => 5, :height => 9 }
```

默认情况下，访问的值如果没有加到 hash 中，会返回 `nil`，意味着，查看 key 的值永远是安全的：

```ruby
my_hash = {}
my_hash[:age] # => nil
```

hash 中的 key 可以是字符串。假如你通过正常的方式访问，将返回 `nil`，你要通过字符类型的 key 来访问：

```ruby
my_hash = { "name" => "user" }
my_hash[:name] # => nil
my_hash["name"] # => user
```

为了解决类似的情况（key 要求或者期望存在），hash 提供了 `fetch` 方法，当访问 key 的值不存在时，它将抛出错误：

```ruby
my_hash = {}
my_hash.fetch(:age) #=> KeyError: key not found: :age
```

`fetch` 方法支持传入第二个参数，用于 `key` 没有设置时，作为返回值：

```ruby
my_hash = {}
my_hash.fetch(:age, 45) #=> => 45
```

`fetch` 也可以接收一个代码块，用于 key 未设置时，作为返回值：

```ruby
my_hash = {}
my_hash.fetch(:age) { 21 } #=> 21

my_hash.fetch(:age) do |k|
  puts "Could not find #{k}"
end
#=> Could not find age
```

hash 支持 `store` 方法作为 `[]=` 方法的别名：

```ruby
my_hash = {}
my_hash.store(:age, 45)
my_hash #=> { :age => 45 }
```

你也可以使用 values 方法来获取所有的值：

```ruby
my_hash = { length: 4, width: 5 }
my_hash.values #=> [4, 5]
```

注意：这是 ruby 2.3+ 版本才有的方法：`dig` 用于嵌套 hash，通过特定的序列来提取 hash 中的值，如果中途任何一步返回了 `nil`，则立马返回 `nil`。

```ruby
h = { foo: {bar: {baz: 1}}}
h.dig(:foo, :bar, :baz) # => 1
h.dig(:foo, :zot, :xyz) # => nil
g = { foo: [10, 11, 12] }
g.dig(:foo, 1) # => 11
```

## 19.4 节 自动创建深度 hash

hash 对于要求存在，但未存在的值有默认值：

```ruby
a = {} p a[ :b ] # => nil
```

在创建 hash 时，可以指定默认值：

```ruby
b = Hash.new 'puppy'
p b[ :b ] # => 'puppy'
```

`Hash.new` 接收一个代码块，它允许你自动创建嵌套 hash。例如 Perl 语言的 autovivification 行为或者 `mkdir -p`：

```ruby
# h is the hash you're creating, and k the key.
#
hash = Hash.new { |h, k| h[k] = Hash.new &h.default_proc }
hash[ :a ][ :b ][ :c ] = 3 p hash # => { a: { b: { c: 3 } } }
```

## 19.5 节 遍历 Hash

Hash 包含了 `Enumerable` 模块，它提供了一些遍历方法，比如：`Enumerable#each`，`Enumerable#each_pair`，`Enumerable#each_key` 以及 `Enumerable#each_value`。

`.each`遍历每一个键值对：

```ruby
h = { "first_name" => "John", "last_name" => "Doe" }
h.each do |key, value|
  puts "#{key} = #{value}"
end
# => first_name = John
# last_name = Doe
```

`.each_key` 只遍历每一个 key：

```ruby
h = { "first_name" => "John", "last_name" => "Doe" }
h.each_key do |key|
  puts key
end
# => first_name
# last_name
```

`.each_value` 只遍历每一个 value：

```ruby
h = { "first_name" => "John", "last_name" => "Doe" }
h.each_value do |value|
  puts value
end
# => John
# Doe
```

`.each_with_index` 遍历每一个元素并提供元素的下标位置：

```ruby
h = { "first_name" => "John", "last_name" => "Doe" }
h.each_with_index do |(key, value), index|
  puts "index: #{index} | key: #{key} | value: #{value}"
end
# => index: 0 | key: first_name | value: John
# index: 1 | key: last_name | value: Doe
```

## 19.6 节 Hash 值过滤

`select` 接收一个代码块，过滤代码块计算为 true，返回一个新的 hash：

```ruby
{ :a => 1, :b => 2, :c => 3 }.select { |k, v| k != :a && v.even? } # => { :b => 2 }
```

当你在过滤代码块中不需要 key 或者 value，使用 `_` 代替：

```ruby
{ :a => 1, :b => 2, :c => 3 }.select { |_, v| v.even? } # => { :b => 2 }
{ :a => 1, :b => 2, :c => 3 }.select { |k, _| k == :c } # => { :c => 3 }
```

`reject` 与 `select` 相反，过滤代码块计算为 false 时，返回新的 hash：

```ruby
{ :a => 1, :b => 2, :c => 3 }.reject { |_, v| v.even? } # => { :a => 1, :c => 3 }
{ :a => 1, :b => 2, :c => 3 }.reject { |k, _| k == :b } # => { :a => 1, :c => 3 }
```

## 19.7 节 数组与 Hash 的转换

Hash 可以轻松地与数组进行转换。Hash 转成数组时，键值对将转换为一个数组，嵌套在外层数组里：

```ruby
{ :a => 1, :b => 2 }.to_a # => [[:a, 1], [:b, 2]]
```

相反，也可以通过类似的格式转换为 Hash：

```ruby
[[:x, 3], [:y, 4]].to_h # => { :x => 3, :y => 4 }
```

可以通过 `Hash[]` 方法来初始化一组交替的键值对为 Hash：

```ruby
Hash[:a, 1, :b, 2] # => { :a => 1, :b => 2 }
```

或者通过嵌套数组：

```ruby
Hash[ [[:x, 3], [:y, 4]] ] # => { :x => 3, :y => 4 }
```

Hash 也可以通过 `flatten` 方法还原成数组：

```ruby
{ :a => 1, :b => 2 }.flatten # => [:a, 1, :b, 2]
```

Hash 和数组的简单转换是通过遍历，例如 `collect` 或者 `zip`

```ruby
Hash[('a'..'z').collect{ |c| [c, c.upcase] }] # => { 'a' => 'A', 'b' => 'B', ... }
people = ['Alice', 'Bob', 'Eve']
height = [5.7, 6.0, 4.9]
Hash[people.zip(height)] # => { 'Alice' => 5.7, 'Bob' => '6.0', 'Eve' => 4.9 }
```

## 19.8 节 重载 hash 方法
Ruby hash 使用 `hash` 和 `eql?` 来执行操作，并将对象存储在 hash 内部 hash bins 中。这两个方法默认实现是 murmur hash function over all member fields of the hashed object。要重写他们的行为，就可能改到 `hash` 和 `eql?` 方法。
与其他实现一样，如果 `a.hash==b.hash`，两个对象 a 和 b 将被哈希到同一个桶中。如果 `a.eql？b (1)` 。因此，当重新实现 `hash` 和 `eql?` 时。一个人应该注意确保如果 a 和 b 在等式 (1) 下相等？它们必须返回相同的哈希值。否则，这可能导致哈希中的重复条目。相反，哈希实现中的错误选择可能会导致许多对象共享相同的哈希桶，有效地破坏了 `O(1)` 查找时间并导致 `O(n)` 调用 `eql?`在所有对象上。

在下面的示例中，只有类 A 的实例作为键存储，因为它是首先添加的：

```ruby
class A
  def initialize(hash_value)
    @hash_value = hash_value
  end
  def hash
    @hash_value # Return the value given externally
  end
  def eql?(b)
    self.hash == b.hash
  end
end
class B < A
end
a = A.new(1)
b = B.new(1)
h = {} h[a] = 1 h[b] = 2
raise "error" unless h.size == 1
raise "error" unless h.include? b
raise "error" unless h.include?
```

## 19.9 节 获取 hash 所有的 key 或者 value

```ruby
{foo: 'bar', biz: 'baz'}.keys # => [:foo, :biz]
{foo: 'bar', biz: 'baz'}.values # => ["bar", "baz"]
{foo: 'bar', biz: 'baz'}.to_a # => [[:foo, "bar"], [:biz, "baz"]]
{foo: 'bar', biz: 'baz'}.each #<Enumerator: {:foo=>"bar", :biz=>"baz"}:each
```

## 19.10 节 修改 hash 的 key 或者 value

你可以通过新建一个 hash 来时间 key 和 value 的更改。实际上，你也可以通过 `inject` 方法。例如，你想把 key 改成字符形式，value 修改成大写的：

```ruby
fruit = { name: 'apple', color: 'green', shape: 'round' }
# => {:name=>"apple", :color=>"green", :shape=>"round"}

new_fruit = fruit.inject({}) { |memo, (k,v)| memo[k.to_s] = v.upcase; memo }
# => new_fruit is {"name"=>"APPLE", "color"=>"GREEN", "shape"=>"ROUND"}
```

Hash 本质上是可以枚举的键值对集合。因此是有类似 `each`、`map` 和 `inject 方法的。

对于 hash 中的每个键/值对的值，第一次运行时的 `memo` 的值是 `seed`，在我们的例子中，传递给注入空哈希 `{}`。`memo` 接下来的值，是上一次计算之后的值，这也是我们更新 `memo` 并在最后返回的原因。最后一次计算的返回值，也是 `inject` 的返回值，在我们的例子中是 `memo`。

如果不需要计算最终的值，你可以使用 `each_with_object`：

```ruby
new_fruit = fruit.each_with_object({}) { |(k,v), memo| memo[k.to_s] = v.upcase }
```

或者用 `map`：

```ruby
# ruby version 1.8+
new_fruit = Hash[fruit.map{ |k,v| [k.to_s,
```

更过细节，参考这里，包括操作 hash。

## 19.11 节 hash 上的集合操作

- 交集
```ruby
hash1 = { :a => 1, :b => 2 }
hash2 = { :b => 2, :c => 3 }
hash1.select { |k, v| (hash2.include?(k) && hash2[k] == v) } # => { :b => 2 }
``

- hash 合并
key 在 hash 中都是不同的。如果两个 hash 中出现了相同的 key，那么它们是可以合并的。被合并的那个的值将被覆盖

```ruby
hash1 = { :a => 1, :b => 2 }
hash2 = { :b => 4, :c => 3 }
hash1.merge(hash2) # => { :a => 1, :b => 4, :c => 3 }
hash2.merge(hash1) # => { :b => 2, :c => 3, :a => 1 }
```