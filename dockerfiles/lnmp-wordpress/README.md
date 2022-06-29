# Dockerized Wordpress
Setup a docker environment for Wordpress with PHP 7.2, nginx 1.15, and MySQL 5.7. This docker setup is to be used for local development and testing.

# Enviroment
Mysql data folder: ./mysql/data (mounted to /var/lib/mysql)

## Usage
1. Download [wordpress-latest](https://cn.wordpress.org/latest-zh_CN.zip) and put the code into the folder "wordpress"
2. Run in the root folder: docker-compose up --build -d

