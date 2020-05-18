# Login to AWS Console
## Return to [README.md](README.md)
1. 在浏览器中，打开链接https://dashboard.eventengine.run/login?hash=xxxxxx
2. 点击“AWS Console”
3. 点击“Open AWS Console”
4. 点击右上角“Services”，然后搜索“Systems Manager”
5. 点击打开Systems Manager
6. 在左面导航栏中，找到Session Manager
7. 点击进入Session Manager
8. 点击右侧“Start Session”
9. 选中“TestInstance”，点击“Start Session”
10. 进入了EC2 instance。
11. 执行下列命令
```
export PS1="\n[\u@\h \W]$ "
cd /home/ssm-user
sudo su
```
