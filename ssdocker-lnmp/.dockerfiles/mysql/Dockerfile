ARG MYSQL_VERSION=latest
FROM mysql:${MYSQL_VERSION}

LABEL maintainer="SwordLee <277850636@qq.com>"

#####################################
# Set Timezone
#####################################

ARG TZ=UTC
ENV TZ ${TZ}
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone && chown -R mysql:root /var/lib/mysql/
RUN mkdir -p /var/log/mysql
RUN chmod -R 777 /var/log/mysql

COPY my.cnf /etc/mysql/conf.d/my.cnf

CMD ["mysqld"]

EXPOSE 3306
