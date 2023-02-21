# 第 24 章 Ruby 中的 `Enumerable` 模块

`Enumerable` 模块--提供一系列在集合中（`Array`、`Hashes`、`Set`、`HashMap`）进行遍历、排序、查找方法等。

## 24.1 节 `Enumerable` 模块

1. 循环:
    ```ruby
      CountriesName = ["India", "Canada", "America", "Iraq"]
      for country in CountriesName
        puts country
      end
    ```
2. Each 迭代器
    同样可以用 `each` 来实现用 `for` 循环
    ```ruby
      CountriesName = ["India", "Canada", "America", "Iraq"]
      CountriesName.each do |country|
        puts country
      end
    ```

    Each 迭代器, 遍历数组上的每一个元素
    `each` ---------- iterator
    `do` ------------ start of the block
    `|country|` ----- argument passed to the block
    `puts country` ----block

3. `each_with_index` 迭代器
    `each_with_index` 迭代器提供了元素及当前迭代元素的下标

    ```ruby
    CountriesName = ["India", "Canada", "America", "Iraq"]
    CountriesName.each_with_index do |country, index|
      puts country + " " + index.to_s
    end
    ```

4. `each_index` 迭代器
    和 `each_with_index` 雷瑟
    ```ruby
    CountriesName = ["India", "Canada", "America", "Iraq"]
    CountriesName.each_index do |index|
      puts index
    end
    ```

5. `map` 迭代器
    `map` 迭代器，通常用于获取变化之后的数组副本，是获取新数组，而不是改变旧数组。

    我们先用 `for` 循环：

    你有一个数组：`arr = [1,2,3,4,5]`；

    你需要生成一个新的数组。

    ```ruby
    arr = [1,2,3,4,5]
    newArr = []
    for x in 0..arr.length-1
      newArr[x] = -arr[x]
    end
    ```

    上述提供的数组，用 `map` 来生成一个新的数组

    ```ruby
    arr = [1,2,3,4,5]
    newArr = arr.map do |x|
      -x
    end

    puts arr
    # [1,2,3,4,5]
    puts newArr
    # [-1, -2, -3, -4, -5]
    ```

    `map` 返回了更新之后的副本，`arr` 没有被修改。

    `each` 和 `map` 之间的区别

    1. `map` 返回更新之后的新数组
        看个例子：

        ```ruby
        arr = [1,2,3,4,5]
        newArr = arr.map do |x|
           puts x
           -x
        end

        puts newArr
        # [-1, -2, -3, -4, -5]
        ```

    `map` 是迭代器，并且能返回被修改之后的副本

    ```ruby
    arr = [1,2,3,4,5]
    newArr = arr.each do |x|
      puts x
      -x
   end

   puts newArr
   # [1,2,3,4,5]
   ```

   `each` 直接返回了原数组，因为它仅仅是遍历。Each 迭代仅仅是迭代，不会修改迭代中的值。

6. `map!` 迭代器
    `map` 修改原数组，并返回修改之后的数组。

    ```ruby
    arr = [1,2,3,4,5]
    arr.map! do |x|
       puts x
       -x
    end
    puts arr
    # [-1, -2, -3, -4, -5]
    ```

7. `map` 和 `each_with_index` 的结合
    `each_with_index` 会遍历集合，`map` 会返回修改之后的数据副本。
    ```ruby
    CountriesName = ["India", "Canada", "America", "Iraq"]
    newArray =
    CountriesName.each_with_index.map do |value, index|
      puts "Value is #{value} and the index is #{index}"
      # "Value is #{value} and the index is #{index}"
    end

    newArray =
    CountriesName.each_with_index.map do |value, index|
      if ((index%2).eql?0)
        puts "Value is #{value} and the index is #{index}"
        # "Value is #{value} and the index is #{index}"
      end
    end
    puts newArray
   # ["Value is India and the index is 0", nil, "Value is America and the index is 2", nil]
    ```

8. `select`
    ```ruby
    MixedArray = [1, "India", 2, "Canada", "America", 4]
    MixedArray.select do |value|
      (value.class).eql?Integer
    end
    ```

    `select` 方法获取满足条件的结果

9. `inject` 方法
    `inject` 方法换算出最终的值

    我们假设你想做求和运算：

    `for` 循环是这么做的：

    ```ruby
    arr = [1,2,3,4,5]
      sum = 0
      for x in 0..arr.length-1
        sum = sum + arr[0]
      end
      puts sum
      # 15
    ```

    上述的计算过程，可以用一个方法来实现

    ```ruby
    arr = [1,2,3,4,5]
    arr.inject(0) do |sum, x|
      puts x
      sum = sum + x
    end
    ```

    `inject(0)` -- 传入 `sum` 的初始值，

    如果不用参数: `sum` 可以取 `sum = arr[0]`

    `sum` -- 每一次迭代结束时，总和等于返回值

    `x` -- 指向当前迭代数据

    `inject` 方法也是一个迭代器

总结：操作数据集合的最好方式，就是充分利用 `Enumerable` 模块的提供的方法。
