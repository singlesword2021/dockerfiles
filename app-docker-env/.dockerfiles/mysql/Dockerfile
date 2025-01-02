
## docker login --username=singlesword@126.com registry.cn-hangzhou.aliyuncs.com
FROM registry.cn-hangzhou.aliyuncs.com/ss-prod/mysql:8.4.3

LABEL maintainer="SwordLee <277850636@qq.com>"

ARG TZ=UTC
ENV TZ ${TZ}
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone && chown -R mysql:root /var/lib/mysql/

COPY my.cnf /etc/mysql/conf.d/my.cnf

RUN chmod 0444 /etc/mysql/conf.d/my.cnf

CMD ["mysqld"]

EXPOSE 3306
