# ${\color{red} \textbf{Docker : Angular App}}$

- ${\color{green} \textbf{Create SG- 22, 80, 8080, 3306}}$
- ${\color{green} \textbf{Launch EC2 Instance}}$

### ${\color{purple} \textbf{Install docker}}$

````
sudo yum update -y
sudo yum install -y docker
sudo service docker start
sudo usermod -a -G docker ec2-user
````

### ${\color{purple} \textbf{Install git}}$
````
yum install git -y
git clone https://github.com/aarti215/Docker-angular-app-project.git
````
# ${\color{red} \textbf{Database}}$

**Go to database Directory**
````
cd Docker-angular-app-project/database
````
**Create Dockerfile for Database**

${\color{green} \textbf{Dockerfile}}$
````
FROM mariadb:10.5

ENV MARIADB_ROOT_PASSWORD=1234
ENV MARIADB_DATABASE=springbackend
ENV MARIADB_USER=admin
ENV MARIADB_PASSWORD=passwd123

COPY springbackend.sql /docker-entrypoint-initdb.d/

EXPOSE 3306
````
**Add two commands at top in ${\color{green} \textbf{springbackend.sql}}$ for database**
````
create database springbackend;
use springbackend;
````
**Build the Image for Database**
````
docker build -t database:d1 .
````
**Run Container for database**
````
docker run -itd --name database -p 3306:3306 database:d1
````

# ${\color{red} \textbf{Spring-Backend}}$

**Go to spring-backend**
````
cd ../spring-backend
````
**Create Dockerfile for spring-backend**


${\color{green} \textbf{Dockerfile}}$
````
FROM ubuntu:latest

RUN apt-get update && \
    apt-get install -y openjdk-8-jdk maven && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY . /app

RUN mvn clean package -Dmaven.test.skip=true

EXPOSE 8080

CMD ["java", "-jar", "target/spring-backend-v1.jar"]
````
**Configure ${\color{green} \textbf{application.properties}}$ file**
````
vim src/main/resources/application.properties
````
**Add public-ip, user, password**
````
spring.datasource.url=jdbc:mysql://<public_ip>:3306/springbackend?useSSL=false
spring.datasource.username=<user>
spring.datasource.password=<password>

spring.jpa.generate-ddl=true
````
**Build the Image for spring-backend**
````
docker build -t backend:b1 .
````
**Run the Container for spring-backend**
````
docker run -itd --name backend -p 8080:8080 backend:b1
````

# ${\color{red} \textbf{Angular-Frontend}}$

**Create the Dockerfile for Angular-Frontend**


${\color{green} \textbf{Dockerfile}}$
````
FROM node:14-alpine as build

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

RUN npm run build 

FROM nginx:alpine

COPY --from=build /app/dist/angular-frontend /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
````

**configure the ${\color{green} \textbf{worker.service.ts}}$ file**

**${\color{red} \textbf{NOTE :}}$ Add public ip of instance**
````
vim src/app/services/worker.service.ts
````

**Build the Image for Angular-Frontend**
````
docker build -t frontend:f1
````
**Run the Container for Angular-Frontend**
````
docker run -itd --name frontend -p 80:80 frontend:f1
````
${\color{red} \textbf{NOTE :}}$ Check the Containers status, its Running or exited.

${\color{green} \textbf{Host the public-ip of Instance}}$
