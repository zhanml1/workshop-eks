# Docker Imager Build

## Return to [README.md](README.md)

## For JAVA Tomcat

### 1. Provision a EC2 instance in AWS ZHY Region.

### 2. download and install tomcat
curl -O https://mirror.bit.edu.cn/apache/tomcat/tomcat-9/v9.0.35/bin/apache-tomcat-9.0.35.tar.gz
tar -xzf apache-tomcat-9.0.35.tar.gz
mv apache-tomcat-9.0.35 tomcat

### 3. dockerfile

from centos:8
WORKDIR /home
COPY tomcat/ /home/tomcat/
RUN yum install -y java-1.8.0-openjdk
EXPOSE 8080
ENTRYPOINT ["/home/tomcat/bin/catalina.sh","run"]

### 4. docker build
docker build -t mytomcat .

### 5. docker run
docker run -d -p 8080:8080 --name mytomcat-container mytomcat
