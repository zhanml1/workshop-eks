# Docker Imager Build

## Return to [README.md](README.md)


## 在EC2 instance中执行下列操作：
将生成JAVA Tomcat的Docker Image


### 1. download and install tomcat
```
curl -O https://mirror.bit.edu.cn/apache/tomcat/tomcat-9/v9.0.35/bin/apache-tomcat-9.0.35.tar.gz
tar -xzf apache-tomcat-9.0.35.tar.gz
mv apache-tomcat-9.0.35 tomcat
```
### 2. make dockerfile
```
from centos:8
WORKDIR /home
COPY tomcat/ /home/tomcat/
RUN yum install -y java-1.8.0-openjdk
EXPOSE 8080
ENTRYPOINT ["/home/tomcat/bin/catalina.sh","run"]
```
### 3. docker build
```
docker build -t mytomcat .
```
### 4. docker run
```
docker run -d -p 8080:8080 --name mytomcat-container mytomcat
```
### 5. upload docker image to ECR

#### 初始化Elastic Container Registry
- 点击左上角“Services”，搜索“Elastic Container Registry”
- 进入Elastic Container Registry
- 点击右侧“get started”
- 在Repository name处，输入test
- 保存URI，如xxxxxx.dkr.ecr.ap-southeast-1.amazonaws.com/test




