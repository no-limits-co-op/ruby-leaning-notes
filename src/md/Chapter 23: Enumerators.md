# 第 23 章 Enumerators

|参数|详情|
|---|---|
<!-- | `yield` | 作为 `yield` 的反馈，别名为 `<<`，执行这 | -->

枚举器，是一个受控的、实现了 `iteration` 的对象。

该对象会根据需要的枚举值，而不是在满足某些条件之前进行循环。循环的执行将被停止，直到对象的所有者请求下一次值为止。

枚举器让无限制值的流成为可能。

## 23.1 节 自定义枚举器

让我们为斐波那契数列创建一个枚举器：

```ruby
fibonacci = Enumerator.new do |yielder|
  a = b = 1
  loop do
    yielder << a
    a, b = b, a + b
  end
end
```

我们现在就可以使用 `fibonacci` 的枚举方法：

```ruby
fibonacci.take 10
# => [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]
```

## 23.2 节 存在的方法

假如一个迭代方法（例如：`each`）被调用时，没有传入代码块，应当返回枚举器。

这个可以通过 `enum_for` 来实现：

```ruby
def each
  return enum_for :each unless block_given?
  yield :x
  yield :y
  yield :z
end
```

这使程序员能够组合出可操作的枚举：

```ruby
each.drop(2).map(&:upcase).first
# => :Z
```

## 23.3 节 Rewinding

使用 `rewinding` 重置枚举器

```ruby
ℕ = Enumerator.new do |yielder|
  x = 0
  loop do
    yielder << x
    x += 1
  end
end
ℕ.next
# => 0
ℕ.next
# => 1
ℕ.next
# => 2
ℕ.rewind
ℕ.next
# => 0
```


