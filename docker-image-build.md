# Docker Imager Build

## Return to [README.md](README.md)


## 在EC2 instance中执行下列操作：
将生成JAVA Tomcat的Docker Image

### 1. install docker
```
yum install -y docker
systemctl start docker
```
### 1. download and install tomcat
```
curl -O http://ftp.meisei-u.ac.jp/mirror/apache/dist/tomcat/tomcat-9/v9.0.35/bin/apache-tomcat-9.0.35.tar.gz
tar -xzf apache-tomcat-9.0.35.tar.gz
mv apache-tomcat-9.0.35 tomcat
```
### 2. make dockerfile
```
cat <<EOF > dockerfile
from centos:8
WORKDIR /home
COPY tomcat/ /home/tomcat/
RUN yum install -y java-1.8.0-openjdk
EXPOSE 8080
ENTRYPOINT ["/home/tomcat/bin/catalina.sh","run"]
EOF
```
### 3. docker build
```
docker build -t mytomcat .
docker images
```
### 4. docker run
```
docker run -d -p 8080:8080 --name mytomcat-container mytomcat
curl http://localhost:8080
```
### 5. upload docker image to ECR

#### 初始化Elastic Container Registry
- 点击左上角“Services”，搜索“Elastic Container Registry”
- 进入Elastic Container Registry
- 点击右侧“get started”
- 在Repository name处，输入mytomcat
- 记录URI，如xxxxxx.dkr.ecr.ap-southeast-1.amazonaws.com/mytomcat

#### upload docker image
```
# update ECR_URI
export ECR_URL=xxxxxx.dkr.ecr.ap-southeast-1.amazonaws.com/mytomcat
$(aws ecr get-login --no-include-email --region ap-southeast-1)
docker tag mytomcat:latest $ECR_URL:1
docker push $ECR_URL:1
```

