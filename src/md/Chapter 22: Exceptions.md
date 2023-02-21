# 第 22 章 Exceptions

## 22.1 节 创建自定义类型的异常

任何一个继承了 `Exception` 的类或者是 `Exception` 的子类，都可以被称为自定义异常。

通常，你应该继承 `StandardError` 或者继承了它的子类。`Exception` 通常用于虚拟机或系统异常，捕获它们以阻止强制中断我们预期的执行。

```ruby
# Defines a new custom exception called FileNotFound
class FileNotFound < StandardError
end
def read_file(path)
 File.exist?(path) || raise(FileNotFound, "File #{path} not found")
 File.read(path)
end
read_file("missing.txt") #=> raises FileNotFound.new("File `missing.txt` not found")
read_file("valid.txt") #=> reads and returns the content of the file
```

通常会在命名异常名称时，以Error结尾：
- ConnectionError
- DontPanicError

但如果异常名称能够准确表明其含义时，就不要再后面加Error了，因为冗余了：
- FileNotFound vsFileNotFoundError
- DatabaseExploded vsDatabaseExplodedError

## 22.2 节 处理多个异常

你可以在 `rescue` 中同时处理多个异常：

```ruby
begin
  # an execution that may fail
rescue FirstError, SecondError => e
  # do something if a FirstError or SecondError occurs
end
```

你可以加多个 `rescue`：

```ruby
begin
 # an execution that may fail
rescue FirstError => e
 # do something if a FirstError occurs
rescue SecondError => e
 # do something if a SecondError occurs
rescue => e
 # do something if a StandardError occurs
end
```

`rescue` 执行的顺序：第一个匹配到的异常先执行。因此，假如你将 `StandardError` 作为第一个异常，且你所有的异常都继承了 `StandardError` 时，那么除了 `StandardError`，其他的异常都不会被捕获到。

```ruby
begin
 # an execution that may fail
rescue => e
 # this will swallow all the errors
rescue FirstError => e
 # do something if a FirstError occurs
rescue SecondError => e
 # do something if a SecondError occurs
end
```

一些代码块有隐式异常，例如 def、class、module。这些代码块允许你不写 `begin` 语句：

```ruby
def foo
 ...
rescue CustomError
 ...
ensure
 ...
end
```

## 22.3 节 异常处理
使用 `begin...rescue` 来捕获和处理异常：

```ruby
begin
	# an execution that may fail
rescue
	# something to execute in case of failure
end
```

`rescue` 语句和其他编程语言，例如，C# 和 Java 中的catch类似。

`rescue` 默认捕获 `StandardError`。

注意：注意避免捕获 `Exception`，而是捕获默认的 `StandardError`。`Exception` 包括 `SystemExit`、`NoMemoryError` 以及一些其他的你不想捕获的异常。不论如何，请考虑捕获 `StandardError`。

你可以指定应该捕获的异常类：

```ruby
begin
 # an excecution that may fail
rescue CustomError
 # something to execute in case of CustomError
 # or descendant
end
```

上面的 `rescue` 不会捕获不是 `CustomError` 的异常。

你可以将异常放进一个变量里面：

```ruby
begin
  # an excecution that may fail
rescue CustomError => error
  # error contains the exception
  puts error.message # provide human-readable details about what went wrong.
  puts error.backtrace.inspect # return an array of strings that represent the call stack
end
```

如果处理异常失败了，你可以在 `rescue` 中的任何位置 `raise` 它：

```ruby
begin
  #here goes your code
rescue => e
  #failed to handle
  raise e
end
```

如果你想再重试一次 `begin`，调用 `retry`：

```ruby
begin
  #here goes your code
rescue StandardError => e
  #for some reason you want to retry you code
  retry
end
```

在 `retry` 的时候可能会陷入死循环。为了避免它，我们明确限制 `retry` 的执行次数：

```ruby
retry_count = 0
begin
  # an excecution that may fail
rescue
  if retry_count < 5
    retry_count = retry_count + 1
    retry
  else
    #retry limit exceeds, do something else
  end
end
```

你可以提供一个 `else` 代码块，或者提供一个 `ensure` 代码块。`else` 代码块在没有捕获到异常时执行。`ensure` 代码块不管有没有捕获到异常都会执行。`ensure` 和 C# 或者 Java 语言中的 `finally` 很像。

```ruby
begin
 # an execution that may fail
rescue
 # something to execute in case of failure
else
 # something to execute in case of success
ensure
 # something to always execute
end
```

如果你在 `def`、`module`、`class` 内部，就没必要使用 `begin` 语句了。

```ruby
def foo
 ...
rescue
 ...
end
```

## 22.4 节 抛出异常

为了抛出异常，向 `Kernel#raise` 方法中传入异常类和（或者）错误信息：

```ruby
raise StandardError # raises a StandardError.new
raise StandardError, "An error" # raises a StandardError.new("An error")
```

你可以简化处理，只传入错误信息。在这种情况下，错误信息会被包装成 `RuntimeError`：

```ruby
raise "An error" # raises a RuntimeError.new("An error")
```

这有一个例子：

```ruby
def hello(subject)
  raise ArgumentError, "`subject` is missing" if subject.to_s.empty?
  puts "Hello #{subject}"
end

hello # => ArgumentError: `subject` is missing
hello("Simone") # => "Hello Simone"
```

## 22.5 节 向（自定义）异常中传入信息

异常中包含一些额外的信息，这可能非常有用，例如，打印日志，或者要在捕获异常的时候做一些处理：

```ruby
class CustomError < StandardError
  attr_reader :safe_to_retry
  def initialize(safe_to_retry = false, message = 'Something went wrong')
    @safe_to_retry = safe_to_retry

    super(message)
  end
end
```

抛出异常：

```ruby
raise CustomError.new(true)
```

捕获异常，访问额外提供的信息：

```ruby
begin
  # do stuff
rescue CustomError => e
  retry if e.safe_to_retry
end
```

