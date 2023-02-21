# Chapter 2：类型转换

## Section 2.1：转浮点型

```ruby
"123.50".to_f #=> 123.5
Float("123.50") #=> 123.5
```

尽管上述结果相同，但当字符串不是一个有效的浮点型存在差别

```ruby
"something".to_f #=> 0.0
Float("something") #=> ArgumentError: invalid value for Float(): "something"
```

## Section 2.2：转字符串

```ruby
123.5.to_s #=> "123.5"
String(123.5) #=> "123.5"
```

通常，String() 只是调用#to_s

Kernel*#sprintf*和String#%变现和C语言中的类似

```ruby
sprintf("%s", 123.5) #=> "123.5"
"%s" % 123.5 #=> "123.5"
"%d" % 123.5 #=> "123.5"
"%.2f" % 123.5 #=> "123.50"
```

## Section 2.3：转整型

```ruby
"123.50".to_i #=> 123
Integer("123.50").to_i #=> 123
```

字符换转整型时，只会从字符串开头处开始转整型，不会从字符串其他部分取整数值

```ruby
"123-foo".to_i #=> 123
"foo-123".to_i #=> 0
```

当字符串不是一个有效的整数时，会有以下区别

```ruby
"something".to_i #=> 0
Integer("something") #=> ArgumentError: invalid value of Integer(): "something"
```

## Section 2.4：浮点型和整型

```ruby
1/2 #=> 0
```

因为做除法的时候用的是两个整数， 所以结果也应该是一个整数。为了解决这个问题，至少要把一个数转换成浮点型

```ruby
fa1.0/2 #=> 0.5
1.to_f / 2 #=> 0.5
1/Float(2) #=> 0.5
```

或者，使用fdiv来返回浮点型的除法结果， 使用它时不需要再做额外的转换。

```ruby
1.fdiv 2 #=> 0.5
```