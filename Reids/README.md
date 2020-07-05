# Redis

## redis 安装
```shell

# 查找redis是否在库中
brew search redis
# 安装redis
brew install redis

# 服务方式启动redis
brew services start redis
# 或者
redis-server /usr/local/etc/redis.conf

# redis配置文件
vi /usr/local/etc/redis.conf
```