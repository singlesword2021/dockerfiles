# 提供统一可快速启动的docker环境

## 初始化方式
1. 复制整个 app-docker-env 目录到某一目录
2. 创建docker volumes目录, 例如：mkdir /var/data/docker-volume
3. 修改.env中的 VOLUMES_PATH_HOST, 设置为上一步创建的目录, 例如：VOLUMES_PATH_HOST=/var/data/docker-volumes/
4. 修改.env中的其它环境变量（如有必要）
5. 修改docker-compose.yml 中的默认端口（如有必要）

## 目录及文件说明
1. ./.dockerfiles		           // 原始dockerfile及配置文件目录，按应用分目录
2. .env                            // 基本环境变量文件，也可以直接写在docker-compose.yml中
3. docker-compose.yml              // 各个service的定义
4. {VOLUMES_PATH_HOST}             // docker volumes 挂载目录，如果只使用local存储，可以不需要挂载

## 服务启动方式
cd app-docker-env &&docker-compose up -d {Service}

## 目前支持的服务
1. mysql
2. nginx (depends on php-fpm)
3. php-fpm
4. bitwarden