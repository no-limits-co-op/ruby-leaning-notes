# 第 5 章 环境变量

## 5.1 节 获取用户配置文件路径示例

```ruby
# will retrieve my home path
ENV['HOME'] # => "/Users/username"
# will try retrieve the 'FOO' environment variable. If failed, will get 'bar'
ENV.fetch('FOO', 'bar')
```
