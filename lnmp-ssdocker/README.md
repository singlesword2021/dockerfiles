# 目录及文件说明
./.dockerfiles		            // 原始dockerfile
./volumes/                      // 待挂载的目录，可以整体移动到其他目录（需要在.env中把 VOLUMES_PATH_HOST 修改到实际路径）,但是结构必须保留
    code/www		            // web代码，php,html,js
    code/vhost		            // nginx的vhost配置文件
    data/mysql		            // mysql的磁盘映射目录
    logs/nginx		            // nginx日志（含php-fpm）
    logs/mysql		            // mysql日志
.env                            // 基本环境变量
docker-compose.yml              // 包含LNMP的各个service

# 初始化操作参考
1. 复制整个 lnmp-ssdocker 目录
2. 将其中的 lnmp-volumes 目录移动到其他位置
3. 修改.env中的 VOLUMES_PATH_HOST, 设置为上一步移动到的目录, 例如：VOLUMES_PATH_HOST=../../docker_volumes/lnmp-volumes
4. 修改docker-compose.yml 中的默认端口（如有必要）


