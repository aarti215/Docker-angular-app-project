FROM mariadb:10.5

ENV MARIADB_ROOT_PASSWORD=1234
ENV MARIADB_DATABASE=springbackend
ENV MARIADB_USER=admin
ENV MARIADB_PASSWORD=passwd123

COPY springbackend.sql /docker-entrypoint-initdb.d/

EXPOSE 3306


