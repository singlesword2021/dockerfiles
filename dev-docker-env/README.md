# 提供统一可快速启动的docker环境

## 启动方式
docker-compose -f docker-compose.yml up -d {Service}

## 使用方式
1. 复制 docker-compose.yml 和 相应的目录（例如：mysql）到某一目录
2. 运行：docker-compose -f docker-compose.yml up -d mysql
3. 注意：默认使用内置的卷, 因此.env中的VOLUMES_PATH_HOST 并未使用

## 当前支持
mysql
