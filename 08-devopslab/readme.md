[TOC]

环境介绍：

* 虚拟实例1：运行jenkins + gitlab + harbor环境，节点地址192.168.14.244（可以把当前机器的地址进行查找替换）
* 虚拟实例2：运行单节点 k8s 群集，版本为1.23.00
* 上述两个实例的配置均为 4vCPU 8GB内存 127GB 硬盘空间，操作系统为 ubuntu 20.04



# DevOps 工具链初始化安装



## Docker 的安装和初始化配置



安装 docker

```bash
apt -y install apt-transport-https ca-certificates curl software-properties-common
```

```bash
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
```

```bash
sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
```



修改 docker 配置文件

```bash
cat > /etc/docker/daemon.json << EOF
{
    "exec-opts": ["native.cgroupdriver=systemd"],
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "100m",
        "max-file": "10"
    },
    "registry-mirrors": ["https://pqbap4ya.mirror.aliyuncs.com"]
}
EOF
```



安装docker docker-compose

```bash
apt install docker-compose
```



下载项目文件（可选）

```bash
git clone https://github.com/cloudzun/devopslab
```





## 部署 Jenkins 



创建文件夹或者进入 jenkins 文件夹

```bash
root@dockerlab:~/devopslab/jenkins# pwd
/root/devopslab/jenkins
```



创建 docker-compose 文件

```bash
nano docker-compose.yaml
```



使用以下范例填充 docker-compose 文件

```yaml
version: '2'
 
services:
 jenkins:
 image: docker.io/bitnami/jenkins:2.319.1-debian-10-r11
 restart: always
 ports:
 - '8080:8080'
 - '50000:50000'
 environment:
 - JENKINS_PASSWORD=password
 - JENKINS_USERNAME=admin
 volumes:
 - 'jenkins_data:/bitnami/jenkins'
 
volumes:
 jenkins_data:
 driver: local
```

备注： 如果已经已经下载了项目文件可以直接使用



安装 Jenkins

```bash
docker-compose up -d
```



```bash
root@dockerlab:~/devopslab/jenkins# docker-compose up -d
Creating network "jenkins_default" with the default driver
Creating volume "jenkins_jenkins_data" with local driver
Pulling jenkins (docker.io/bitnami/jenkins:2.319.1-debian-10-r11)...
2.319.1-debian-10-r11: Pulling from bitnami/jenkins
0796bf144e3f: Pull complete
0796bf144e3f: Pull complete
e85548e986ad: Pull complete
5914dad9d0cc: Pull complete
9005e670c8c9: Pull complete
1265095ed0c0: Pull complete
658038a23762: Pull complete
903979d96da5: Pull complete
49a3b0edd7f5: Pull complete
0c32ce46b69c: Pull complete
9f645f0ad478: Pull complete
a9c894506ad3: Pull complete
Digest: sha256:4413df5d6e3b71f2994cae0e541323c3b6b7d1ce1babe3eba6999c3f33e2154a
Status: Downloaded newer image for bitnami/jenkins:2.319.1-debian-10-r11
Creating jenkins_jenkins_1 ... done
```



查看安装进度

```bash
docker logs -f jenkins_jenkins_1
```



```bash
...
2022-12-27 01:54:50.726+0000 [id=26]    INFO    jenkins.InitReactorRunner$1#onAttained: Prepared all plugins
2022-12-27 01:54:50.818+0000 [id=27]    INFO    jenkins.InitReactorRunner$1#onAttained: Started all plugins
2022-12-27 01:54:52.523+0000 [id=26]    INFO    jenkins.InitReactorRunner$1#onAttained: Augmented all extensions
2022-12-27 01:54:53.553+0000 [id=27]    INFO    jenkins.InitReactorRunner$1#onAttained: System config loaded
2022-12-27 01:54:53.553+0000 [id=27]    INFO    jenkins.InitReactorRunner$1#onAttained: System config adapted
2022-12-27 01:54:53.554+0000 [id=27]    INFO    jenkins.InitReactorRunner$1#onAttained: Loaded all jobs
2022-12-27 01:54:53.581+0000 [id=27]    INFO    jenkins.InitReactorRunner$1#onAttained: Configuration for all jobs updated
2022-12-27 01:54:53.695+0000 [id=40]    INFO    hudson.model.AsyncPeriodicWork#lambda$doRun$1: Started Download metadata
2022-12-27 01:54:53.863+0000 [id=27]    INFO    jenkins.InitReactorRunner$1#onAttained: Completed initialization
2022-12-27 01:54:53.921+0000 [id=20]    INFO    hudson.WebAppMain$3#run: Jenkins is fully up and running
```

等待看到 `Jenkins is fully up and running` 字样说明安装成功



使用 admin/password 登录到[http://192.168.14.244:8080](https://link.zhihu.com/?target=http%3A//192.168.14.244%3A8080),建议修改密码

![image-20221227095731568](readme.assets/image-20221227095731568.png)



加载jenkins插件:[http://192.168.14.244:8080/pluginManager/available](https://link.zhihu.com/?target=http%3A//192.168.14.244%3A8080/pluginManager/available)

* Git
* Git Parameter
* Git Pipeline for Blue Ocean
* GitLab
* Credentials
* Credentials Binding
* Blue Ocean
* Blue Ocean Pipeline Editor
* Blue Ocean Core JS
* Pipeline SCM API for Blue Ocean
* Dashboard for Blue Ocean
* Build With Parameters
* Dynamic Extended Choice Parameter
* Extended Choice Parameter
* List Git Branches Parameter
* Pipeline
* Pipeline: Declarative
* Kubernetes
* Kubernetes CLI
* Kubernetes Credentials
* Image Tag Parameter
* Active Choices



![image-20221227095821990](readme.assets/image-20221227095821990.png)





![image-20221227095835090](readme.assets/image-20221227095835090.png)



![image-20221227095856226](readme.assets/image-20221227095856226.png)



## 部署 GitLab



创建 gitlab 目录或进入目录

```bash
root@dockerlab:~/devopslab/gitlab# pwd
/root/devopslab/gitlab
```



创建 docker-compose 文件

```bash
nano docker-compose.yaml
```



使用以下代码填充 docker-compose 文件

```yaml
version: '3.6'
services:
  Gitlab:
    image: 'gitlab/gitlab-ce:latest'
    restart: always
    hostname: 'gitlab.admin.com'
    environment:
      TZ: 'Asia/Shanghai'
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://192.168.14.244'
        gitlab_rails['gitlab_ssh_host'] = '192.168.14.244'
        gitlab_rails['gitlab_shell_ssh_port'] = 10022
        prometheus_monitoring['enable'] = false
    ports:
      - '80:80'
      - '10443:443'
      - '10022:22'
    volumes:
      - '/gitlab-data/config:/etc/gitlab'
      - '/gitlab-data/logs:/var/log/gitlab'
      - '/gitlab-data/data:/var/opt/gitlab'
```



安装 gitlab

```bash
docker-compose up -d 
```



```bash
root@dockerlab:~/devopslab/gitlab# docker-compose up -d
Creating network "gitlab_default" with the default driver
Pulling Gitlab (gitlab/gitlab-ce:latest)...
latest: Pulling from gitlab/gitlab-ce
7b1a6ab2e44d: Pull complete
6c37b8f20a77: Pull complete
f50912690f18: Pull complete
bb6bfd78fa06: Pull complete
2c03ae575fcd: Pull complete
839c111a7d43: Pull complete
4989fee924bc: Pull complete
666a7fb30a46: Pull complete
Digest: sha256:5a0b03f09ab2f2634ecc6bfeb41521d19329cf4c9bbf330227117c048e7b5163
Status: Downloaded newer image for gitlab/gitlab-ce:latest
Creating gitlab_Gitlab_1 ... done
```



查看安装进度，确认 gitlab 容器的状态为：healthy

```bash
docker ps
```



```bash
root@dockerlab:~/devopslab/gitlab# docker ps
CONTAINER ID   IMAGE                                   COMMAND                  CREATED          STATUS                   PORTS                                                                                                                   NAMES
a5001f2f81a8   gitlab/gitlab-ce:latest                 "/assets/wrapper"        3 minutes ago    Up 3 minutes (healthy)   0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:10022->22/tcp, :::10022->22/tcp, 0.0.0.0:10443->443/tcp, :::10443->443/tcp   gitlab_Gitlab_1
90e7c9045552   bitnami/jenkins:2.319.1-debian-10-r11   "/opt/bitnami/script…"   26 minutes ago   Up 7 minutes             0.0.0.0:8080->8080/tcp, :::8080->8080/tcp, 0.0.0.0:50000->50000/tcp, :::50000->50000/tcp, 8443/tcp                      jenkins_jenkins_1
```



查看初始化密码

```bash
nano /gitlab-data/config/initial_root_password
```



```bash
# WARNING: This value is valid only in the following conditions
#          1. If provided manually (either via `GITLAB_ROOT_PASSWORD` environment variable or via `gitlab_rails['initial_root_password']`>
#          2. Password hasn't been changed manually, either via UI or via command line.
#
#          If the password shown here doesn't work, you must reset the admin password following https://docs.gitlab.com/ee/security/reset>

Password: +fZLbARlex/yriLeVG71RSfSxEMKkBxKkdysWB82h+E=

# NOTE: This file will be automatically deleted in the first reconfigure run after 24 hours.
```



使用root和初始化密码登录到Gitlab Portal http://192.168.14.244 ，并修改密码

![image-20221227102713071](readme.assets/image-20221227102713071.png)




创建一个公开的组，比如 `kubernetes`

![image-20221227102725644](readme.assets/image-20221227102725644.png)



![image-20221227102739173](readme.assets/image-20221227102739173.png)



从 github 上导入一个项目（比如：https://github.com/cloudzun/cloudzun）进行测试



![image-20221227102750638](readme.assets/image-20221227102750638.png)





![image-20221227102800120](readme.assets/image-20221227102800120.png)



![image-20221227102809829](readme.assets/image-20221227102809829.png)



测试 gitlab 项目

```bash
git clone http://192.168.14.244/kubernetes/cloudzun.git
```



```bash
git config --global user.email info@cloudzun.com
git config --global user.name "Abraham Cheng"
```



针对文件做些修改，或者增加一些文件

```bash
git commit -am "first commit"
git push
```



```bash
root@dockerlab:~/devopslab/gitlab# cd
root@dockerlab:~# git clone http://192.168.14.244/kubernetes/cloudzun.git
Cloning into 'cloudzun'...
remote: Enumerating objects: 200, done.
remote: Total 200 (delta 0), reused 0 (delta 0), pack-reused 200
Receiving objects: 100% (200/200), 39.87 KiB | 7.97 MiB/s, done.
Resolving deltas: 100% (61/61), done.
root@dockerlab:~# git config --global user.email info@cloudzun.com
root@dockerlab:~# git config --global user.name "Abraham Cheng"
root@dockerlab:~# dir
cloudzun  devopslab  readme
root@dockerlab:~# cd cloudzun
root@dockerlab:~/cloudzun# nano index.html
root@dockerlab:~/cloudzun# git commit -am "first commit"
[master 1e16894] first commit
 1 file changed, 1 insertion(+), 1 deletion(-)
root@dockerlab:~/cloudzun# git push
Username for 'http://192.168.14.244': root
Password for 'http://root@192.168.14.244':
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 4 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 318 bytes | 318.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0)
To http://192.168.14.244/kubernetes/cloudzun.git
   dbae153..1e16894  master -> master
```



![image-20221227104426024](readme.assets/image-20221227104426024.png)



## 部署 Harbor



下载安装文件

```bash
wget https://github.com/goharbor/harbor/releases/download/v2.4.3/harbor-offline-installer-v2.4.3.tgz
```



解压

```bash
tar xf harbor-offline-installer-v2.4.3.tgz
```



进入目录

```bash
cd harbor
```



```bash
root@dockerlab:~# cd harbor
root@dockerlab:~/harbor#
```



加载映像文件

```bash
docker load -i harbor.v2.4.3.tar.gz
```



查看映像文件

```bash
docker images | grep goharbor
```



```bash
root@dockerlab:~/harbor# docker images | grep goharbor
goharbor/harbor-exporter        v2.4.3                  776ac6ee91f4   4 months ago    81.5MB
goharbor/chartmuseum-photon     v2.4.3                  f39a9694988d   4 months ago    172MB
goharbor/redis-photon           v2.4.3                  b168e9750dc8   4 months ago    154MB
goharbor/trivy-adapter-photon   v2.4.3                  a406a715461c   4 months ago    251MB
goharbor/notary-server-photon   v2.4.3                  da89404c7cf9   4 months ago    109MB
goharbor/notary-signer-photon   v2.4.3                  38468ac13836   4 months ago    107MB
goharbor/harbor-registryctl     v2.4.3                  61243a84642b   4 months ago    135MB
goharbor/registry-photon        v2.4.3                  9855479dd6fa   4 months ago    77.9MB
goharbor/nginx-photon           v2.4.3                  0165c71ef734   4 months ago    44.4MB
goharbor/harbor-log             v2.4.3                  57ceb170dac4   4 months ago    161MB
goharbor/harbor-jobservice      v2.4.3                  7fea87c4b884   4 months ago    219MB
goharbor/harbor-core            v2.4.3                  d864774a3b8f   4 months ago    197MB
goharbor/harbor-portal          v2.4.3                  85f00db66862   4 months ago    53.4MB
goharbor/harbor-db              v2.4.3                  7693d44a2ad6   4 months ago    225MB
goharbor/prepare                v2.4.3                  c882d74725ee   4 months ago    268MB
```





创建配置文件

```bash
cp harbor.yml.tmpl harbor.yml
```



```bash
root@dockerlab:~/harbor# cp harbor.yml.tmpl harbor.yml
root@dockerlab:~/harbor# dir
common.sh  harbor.v2.4.3.tar.gz  harbor.yml  harbor.yml.tmpl  install.sh  LICENSE  prepare
```



修改配置文件

```bash
nano harbor.yml
```



```bash
# Configuration file of Harbor

# The IP address or hostname to access admin UI and registry service.
# DO NOT use localhost or 127.0.0.1, because Harbor needs to be accessed by external clients.
hostname: 192.168.14.244 #主机名替换为 IP 地址

# http related config
http:
  # port for http, default is 80. If https enabled, this port will redirect to https port
  port: 5000 # 端口替换为 5000

# https related config # HTTPS 这一段都注释掉
# https:
  # https port for harbor, default is 443
  # port: 443
  # The path of cert and key files for nginx
  # certificate: /your/certificate/path
  # private_key: /your/private/key/path
...
```



```bash
...
# The initial password of Harbor admin
# It only works in first time to install harbor
# Remember Change the admin password from UI after launching Harbor.
harbor_admin_password: Harbor12345 #初始密码,可更换

# Harbor DB configuration
database:
  # The password for the root user of Harbor DB. Change this before any production use.
  password: root123
  # The maximum number of connections in the idle connection pool. If it <=0, no idle connections are retained.
  max_idle_conns: 100
  # The maximum number of open connections to the database. If it <= 0, then there is no limit on the number of open connections.
  # Note: the default number of connections is 1024 for postgres of harbor.
  max_open_conns: 900

# The default data volume
data_volume: /data/harbor #修改地址
...
```




预配存储

```bash
mkdir /data/harbor /var/log/harbor -p
 ./prepare
```



```bash
root@dockerlab:~/harbor# mkdir /data/harbor /var/log/harbor -p
root@dockerlab:~/harbor#  ./prepare
prepare base dir is set to /root/harbor
WARNING:root:WARNING: HTTP protocol is insecure. Harbor will deprecate http protocol in the future. Please make sure to upgrade to https
Generated configuration file: /config/portal/nginx.conf
Generated configuration file: /config/log/logrotate.conf
Generated configuration file: /config/log/rsyslog_docker.conf
Generated configuration file: /config/nginx/nginx.conf
Generated configuration file: /config/core/env
Generated configuration file: /config/core/app.conf
Generated configuration file: /config/registry/config.yml
Generated configuration file: /config/registryctl/env
Generated configuration file: /config/registryctl/config.yml
Generated configuration file: /config/db/env
Generated configuration file: /config/jobservice/env
Generated configuration file: /config/jobservice/config.yml
Generated and saved secret to file: /data/secret/keys/secretkey
Successfully called func: create_root_cert
Generated configuration file: /compose_location/docker-compose.yml
Clean up the input dir
```



安装 harbor

```bash
 ./install.sh
```



```bash
root@dockerlab:~/harbor# ./install.sh

[Step 0]: checking if docker is installed ...

Note: docker version: 20.10.22

[Step 1]: checking docker-compose is installed ...

Note: docker-compose version: 1.25.0

[Step 2]: loading Harbor images ...
Loaded image: goharbor/trivy-adapter-photon:v2.4.3
Loaded image: goharbor/redis-photon:v2.4.3
Loaded image: goharbor/nginx-photon:v2.4.3
Loaded image: goharbor/notary-signer-photon:v2.4.3
Loaded image: goharbor/prepare:v2.4.3
Loaded image: goharbor/harbor-registryctl:v2.4.3
Loaded image: goharbor/harbor-log:v2.4.3
Loaded image: goharbor/harbor-jobservice:v2.4.3
Loaded image: goharbor/harbor-exporter:v2.4.3
Loaded image: goharbor/registry-photon:v2.4.3
Loaded image: goharbor/notary-server-photon:v2.4.3
Loaded image: goharbor/harbor-portal:v2.4.3
Loaded image: goharbor/harbor-core:v2.4.3
Loaded image: goharbor/harbor-db:v2.4.3
Loaded image: goharbor/chartmuseum-photon:v2.4.3


[Step 3]: preparing environment ...

[Step 4]: preparing harbor configs ...
prepare base dir is set to /root/harbor
WARNING:root:WARNING: HTTP protocol is insecure. Harbor will deprecate http protocol in the future. Please make sure to upgrade to https
Clearing the configuration file: /config/jobservice/env
Clearing the configuration file: /config/jobservice/config.yml
Clearing the configuration file: /config/db/env
Clearing the configuration file: /config/registryctl/env
Clearing the configuration file: /config/registryctl/config.yml
Clearing the configuration file: /config/core/env
Clearing the configuration file: /config/core/app.conf
Clearing the configuration file: /config/portal/nginx.conf
Clearing the configuration file: /config/log/rsyslog_docker.conf
Clearing the configuration file: /config/log/logrotate.conf
Clearing the configuration file: /config/nginx/nginx.conf
Clearing the configuration file: /config/registry/passwd
Clearing the configuration file: /config/registry/config.yml
Generated configuration file: /config/portal/nginx.conf
Generated configuration file: /config/log/logrotate.conf
Generated configuration file: /config/log/rsyslog_docker.conf
Generated configuration file: /config/nginx/nginx.conf
Generated configuration file: /config/core/env
Generated configuration file: /config/core/app.conf
Generated configuration file: /config/registry/config.yml
Generated configuration file: /config/registryctl/env
Generated configuration file: /config/registryctl/config.yml
Generated configuration file: /config/db/env
Generated configuration file: /config/jobservice/env
Generated configuration file: /config/jobservice/config.yml
loaded secret from file: /data/secret/keys/secretkey
Generated configuration file: /compose_location/docker-compose.yml
Clean up the input dir



[Step 5]: starting Harbor ...
Creating network "harbor_harbor" with the default driver
Creating harbor-log ... done
Creating harbor-db     ... done
Creating redis         ... done
Creating registry      ... done
Creating harbor-portal ... done
Creating registryctl   ... done
Creating harbor-core   ... done
Creating harbor-jobservice ... done
Creating nginx             ... done
✔ ----Harbor has been installed and started successfully.----
```


验证安装过程

```bash
docker ps
```



```bash
root@dockerlab:~/harbor# docker ps
CONTAINER ID   IMAGE                                   COMMAND                  CREATED             STATUS                    PORTS                                                                                                                   NAMES
cc909048817d   goharbor/harbor-jobservice:v2.4.3       "/harbor/entrypoint.…"   2 minutes ago       Up 2 minutes (healthy)                                                                                                                            harbor-jobservice
fd87f71974d0   goharbor/nginx-photon:v2.4.3            "nginx -g 'daemon of…"   2 minutes ago       Up 2 minutes (healthy)    0.0.0.0:5000->8080/tcp, :::5000->8080/tcp                                                                               nginx
85f0a4ace04e   goharbor/harbor-core:v2.4.3             "/harbor/entrypoint.…"   2 minutes ago       Up 2 minutes (healthy)                                                                                                                            harbor-core
3815ba1ef3c6   goharbor/harbor-registryctl:v2.4.3      "/home/harbor/start.…"   2 minutes ago       Up 2 minutes (healthy)                                                                                                                            registryctl
6cfc0338dba3   goharbor/harbor-portal:v2.4.3           "nginx -g 'daemon of…"   2 minutes ago       Up 2 minutes (healthy)                                                                                                                            harbor-portal
14a9dac44b21   goharbor/registry-photon:v2.4.3         "/home/harbor/entryp…"   2 minutes ago       Up 2 minutes (healthy)                                                                                                                            registry
aab0a9c065b6   goharbor/redis-photon:v2.4.3            "redis-server /etc/r…"   2 minutes ago       Up 2 minutes (healthy)                                                                                                                            redis
bb0b750bcdc6   goharbor/harbor-db:v2.4.3               "/docker-entrypoint.…"   2 minutes ago       Up 2 minutes (healthy)                                                                                                                            harbor-db
44629375c6c8   goharbor/harbor-log:v2.4.3              "/bin/sh -c /usr/loc…"   2 minutes ago       Up 2 minutes (healthy)    127.0.0.1:1514->10514/tcp                                                                                               harbor-log
a5001f2f81a8   gitlab/gitlab-ce:latest                 "/assets/wrapper"        49 minutes ago      Up 49 minutes (healthy)   0.0.0.0:80->80/tcp, :::80->80/tcp, 0.0.0.0:10022->22/tcp, :::10022->22/tcp, 0.0.0.0:10443->443/tcp, :::10443->443/tcp   gitlab_Gitlab_1
90e7c9045552   bitnami/jenkins:2.319.1-debian-10-r11   "/opt/bitnami/script…"   About an hour ago   Up 53 minutes             0.0.0.0:8080->8080/tcp, :::8080->8080/tcp, 0.0.0.0:50000->50000/tcp, :::50000->50000/tcp, 8443/tcp                      jenkins_jenkins_1
```




登录到 harbor portal http://192.168.14.244:5000

![image-20221227111137127](readme.assets/image-20221227111137127.png)


创建一个公共项目 `chengzh`

![image-20221227111159786](readme.assets/image-20221227111159786.png)



测试映像上传, 首先需要修改当前机器的 daemon.json

```bash
cat > /etc/docker/daemon.json << EOF
{
 "exec-opts": ["native.cgroupdriver=systemd"],
 "log-driver": "json-file",
 "log-opts": {
 "max-size": "100m",
 "max-file": "10"
 },
 "registry-mirrors": ["https://pqbap4ya.mirror.aliyuncs.com"],
 "insecure-registries": ["192.168.14.244:5000"]
}
EOF
```

注意: 因为 harbor 没采用 https 模式,因此需要增加  `"insecure-registries": ["192.168.14.244:5000"]`



重启docker

```bash
systemctl restart docker
systemctl enable docker
```



```bash
root@dockerlab:~/harbor# systemctl restart docker
root@dockerlab:~/harbor# systemctl enable docker
Synchronizing state of docker.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable docker
```

建议使用另外一台机器测试,如果是在 devops 本机测试,记得这一步之后需要手动拉起 harbor stack



登录到harbor

```bash
docker login 192.168.14.244:5000
```



```bash
root@dockerlab:~/harbor# docker login 192.168.14.244:5000
Username: admin
Password:
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
root@dockerlab:~/harbor#
```



上传映像

```bash
docker pull alexwhen/docker-2048
```



```bash
docker tag alexwhen/docker-2048 192.168.14.244:5000/chengzh/docker-2048
```



```bash
docker push 192.168.14.244:5000/chengzh/docker-2048
```



```bash
root@dockerlab:~/harbor# docker tag alexwhen/docker-2048 192.168.14.244:5000/chengzh/docker-2048
root@dockerlab:~/harbor# docker push 192.168.14.244:5000/chengzh/docker-2048
Using default tag: latest
The push refers to repository [192.168.14.244:5000/chengzh/docker-2048]
5f70bf18a086: Pushed
7c624c88ed95: Pushed
41b3adcd63c6: Pushed
745737c319fa: Pushed
latest: digest: sha256:ecfb0206e8b62c29bd737bf553420f2f460107595f2c5b4671af0a5bfd8367df size: 1567
```



查看上传的映像

![image-20221227112258590](readme.assets/image-20221227112258590.png)



可选,在另一台机器上测试这个映像

```bash
docker run -d -p 2048:80  192.168.14.244:5000/chengzh/docker-2048
```





## K8S 群集的初始化配置



首先按照常规步骤安装 单节点 K8S 群集



删除master污点，使其能承载工作负载

```bash
kubectl taint node node node-role.kubernetes.io/master-
```

注意：node为充当master这台机器的计算机名



打印admin.conf文件内容，并复制保存到本地

```bash
cat $HOME/.kube/config
```



创建ingress

```bash
kubectl apply -f https://raw.githubusercontent.com/cloudzun/devopslab/main/ingress.yaml
```



修改 daemon.json

```bash
cat > /etc/docker/daemon.json << EOF
{
 "exec-opts": ["native.cgroupdriver=systemd"],
 "log-driver": "json-file",
 "log-opts": {
 "max-size": "100m",
 "max-file": "10"
 },
 "registry-mirrors": ["https://pqbap4ya.mirror.aliyuncs.com"],
 "insecure-registries": ["192.168.14.244:5000"]
}
EOF
```



重启 docker

```bash
systemctl restart docker
systemctl enable docker
```





# DevOps 工具链整合配置



## 配置 Jenkins



配置jenkins location (在 http://192.168.14.244:8080/configure )

确认 `Jenkins URL`

![image-20221227132529493](readme.assets/image-20221227132529493.png)



配置 `agent` (在http://192.168.14.244:8080/configureSecurity/ )
启用 `50000` 端口

![image-20221227132617774](readme.assets/image-20221227132617774.png)



配置凭据: (http://192.168.14.244:8080/credentials/store/system/domain/_/)

配置kubernetes证书（ kind: `secret file` id: `study-kubernetes`）

![image-20221227132712116](readme.assets/image-20221227132712116.png)



配置 Harbor 账号密码 `id: HARBOR\_ACCOUNT`

![image-20221227132746457](readme.assets/image-20221227132746457.png)


配置gitlab key(可选)



创建cloud (http://192.168.14.244:8080/configureClouds/ )

 使用 id：`study-kubernetes`

![image-20221227132927966](readme.assets/image-20221227132927966.png)



## 配置 Harbor



创建公共项目：`kubernetes`





## 配置 GitLab



创建公共组：`kubernetes`
在此前创建的公共项目组内导入项目：：https://github.com/cloudzun/spring-boot-project

![image-20221227133609927](readme.assets/image-20221227133609927.png)

注意：新导入的公共项目组的地址：[http://192.168.14.244/kubernetes/spring-boot-project.git]



对项目中的 Jenkins 文件做必要调整:

```yaml
pipeline {
  agent {
    kubernetes {
      cloud 'study-kubernetes'
      slaveConnectTimeout 1200
      workspaceVolume hostPathWorkspaceVolume(hostPath: "/opt/workspace", readOnly: false)
      yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
    - args: [\'$(JENKINS_SECRET)\', \'$(JENKINS_NAME)\']
      image: 'registry.cn-beijing.aliyuncs.com/citools/jnlp:alpine'
      name: jnlp
      imagePullPolicy: IfNotPresent
      volumeMounts:
        - mountPath: "/etc/localtime"
          name: "localtime"
          readOnly: false     
    - command:
        - "cat"
      env:
        - name: "LANGUAGE"
          value: "en_US:en"
        - name: "LC_ALL"
          value: "en_US.UTF-8"
        - name: "LANG"
          value: "en_US.UTF-8"
      image: "registry.cn-beijing.aliyuncs.com/citools/maven:3.5.3"
      imagePullPolicy: "IfNotPresent"
      name: "build"
      tty: true
      volumeMounts:
        - mountPath: "/etc/localtime"
          name: "localtime"
        - mountPath: "/root/.m2/"
          name: "cachedir"
          readOnly: false
    - command:
        - "cat"
      env:
        - name: "LANGUAGE"
          value: "en_US:en"
        - name: "LC_ALL"
          value: "en_US.UTF-8"
        - name: "LANG"
          value: "en_US.UTF-8"
      image: "registry.cn-beijing.aliyuncs.com/citools/kubectl:self-1.17"
      imagePullPolicy: "IfNotPresent"
      name: "kubectl"
      tty: true
      volumeMounts:
        - mountPath: "/etc/localtime"
          name: "localtime"
          readOnly: false
    - command:
        - "cat"
      env:
        - name: "LANGUAGE"
          value: "en_US:en"
        - name: "LC_ALL"
          value: "en_US.UTF-8"
        - name: "LANG"
          value: "en_US.UTF-8"
      image: "registry.cn-beijing.aliyuncs.com/citools/docker:19.03.9-git"
      imagePullPolicy: "IfNotPresent"
      name: "docker"
      tty: true
      volumeMounts:
        - mountPath: "/etc/localtime"
          name: "localtime"
          readOnly: false
        - mountPath: "/var/run/docker.sock"
          name: "dockersock"
          readOnly: false
  restartPolicy: "Never"
  nodeSelector:
    build: "true"
  securityContext: {}
  volumes:
    - hostPath:
        path: "/var/run/docker.sock"
      name: "dockersock"
    - hostPath:
        path: "/usr/share/zoneinfo/Asia/Shanghai"
      name: "localtime"
    - name: "cachedir"
      hostPath:
        path: "/opt/m2"
'''
    }   
}
  stages {
    stage('Pulling Code') {
      parallel {
        stage('Pulling Code by Jenkins') {
          when {
            expression {
              env.gitlabBranch == null
            }

          }
          steps {
            git(changelog: true, poll: true, url: 'http://192.168.14.244/kubernetes/spring-boot-project.git', branch: "${BRANCH}", credentialsId: 'gitlab')
            script {
              COMMIT_ID = sh(returnStdout: true, script: "git log -n 1 --pretty=format:'%h'").trim()
              TAG = BUILD_TAG + '-' + COMMIT_ID
              println "Current branch is ${BRANCH}, Commit ID is ${COMMIT_ID}, Image TAG is ${TAG}"
              
            }

          }
        }

        stage('Pulling Code by trigger') {
          when {
            expression {
              env.gitlabBranch != null
            }

          }
          steps {
            git(url: 'http://192.168.14.244/kubernetes/spring-boot-project.git', branch: env.gitlabBranch, changelog: true, poll: true, credentialsId: 'gitlab')
            script {
              COMMIT_ID = sh(returnStdout: true, script: "git log -n 1 --pretty=format:'%h'").trim()
              TAG = BUILD_TAG + '-' + COMMIT_ID
              println "Current branch is ${env.gitlabBranch}, Commit ID is ${COMMIT_ID}, Image TAG is ${TAG}"
            }

          }
        }

      }
    }

    stage('Building') {
      steps {
        container(name: 'build') {
            sh """ 
              curl repo.maven.apache.org
              mvn clean install -DskipTests
              ls target/*
            """
        }
      }
    }

    stage('Docker build for creating image') {
      environment {
        HARBOR_USER     = credentials('HARBOR_ACCOUNT')
    }
      steps {
        container(name: 'docker') {
          sh """
          echo ${HARBOR_USER_USR} ${HARBOR_USER_PSW} ${TAG}
          docker build -t ${HARBOR_ADDRESS}/${REGISTRY_DIR}/${IMAGE_NAME}:${TAG} .
          docker login -u ${HARBOR_USER_USR} -p ${HARBOR_USER_PSW} ${HARBOR_ADDRESS}
          docker push ${HARBOR_ADDRESS}/${REGISTRY_DIR}/${IMAGE_NAME}:${TAG}
          """
        }
      }
    }

    stage('Deploying to K8s') {
      environment {
        MY_KUBECONFIG = credentials('study-kubernetes')
    }
      steps {
        container(name: 'kubectl'){
           sh """
           /usr/local/bin/kubectl --kubeconfig $MY_KUBECONFIG set image deploy -l app=${IMAGE_NAME} ${IMAGE_NAME}=${HARBOR_ADDRESS}/${REGISTRY_DIR}/${IMAGE_NAME}:${TAG} -n $NAMESPACE
           """
        }
      }
    }

  }
  environment {
    COMMIT_ID = ""
    HARBOR_ADDRESS = "192.168.14.244:5000"
    REGISTRY_DIR = "kubernetes"
    IMAGE_NAME = "spring-boot-project"
    NAMESPACE = "kubernetes"
    TAG = ""
  }
  parameters {
    gitParameter(branch: '', branchFilter: 'origin/(.*)', defaultValue: '', description: 'Branch for build and deploy', name: 'BRANCH', quickFilterEnabled: false, selectedValue: 'NONE', sortMode: 'NONE', tagFilter: '*', type: 'PT_BRANCH')
  }
}
```

-  104行 123行的项目`url:` 地址更新为当前项目地址
- 180行的 `HARBOR\_ADDRESS` 替换位当前 harbor 地址（如果未使用80端口，则需要替换端口）





## 配置 K8S



给节点打标签

```bash
kubectl label node node build=true
```



创建命名空间

```bash
kubectl create ns kubernetes
```



创建包含映像库用户名密码的secret

```bash
kubectl create secret docker-registry harborkey --docker-server=192.168.14.244:5000 --docker-username=admin --docker-password=2wsx#EDC --docker-email=info@cloudzun.com -n kubernetes
```



查看secret

```bash
kubectl get secret -n kubernetes
kubectl describe secret harborkey  -n kubernetes
```



```bash
root@node:~# kubectl get secret -n kubernetes
NAME                  TYPE                                  DATA   AGE
default-token-rzjkb   kubernetes.io/service-account-token   3      82d
harborkey             kubernetes.io/dockerconfigjson        1      82d
root@node:~# kubectl describe secret harborkey  -n kubernetes
Name:         harborkey
Namespace:    kubernetes
Labels:       <none>
Annotations:  <none>

Type:  kubernetes.io/dockerconfigjson

Data
====
.dockerconfigjson:  134 bytes
```





# 构建脚手架项目



## 准备 K8S 资源



创建定义资源所需的配置文件

```bash
nano spring-boot-project.yaml
```



```yaml
---
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: spring-boot-project
  name: spring-boot-project
  namespace: kubernetes
spec:
  ports:
  - name: web
    port: 8761
    protocol: TCP
    targetPort: 8761
  selector:
    app: spring-boot-project
  sessionAffinity: None
  type: ClusterIP
status:
  loadBalancer: {}
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  creationTimestamp: null
  name: spring-boot-project
  namespace: kubernetes
spec:
  rules:
  - host: spring-boot-project.test.com
    http:
      paths:
      - backend:
          service:
            name: spring-boot-project
            port: 
              number: 8761
        path: /
        pathType: ImplementationSpecific
status:
  loadBalancer: {}
---
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: spring-boot-project
  name: spring-boot-project
  namespace: kubernetes
spec:
  replicas: 1
  selector:
    matchLabels:
      app: spring-boot-project
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: spring-boot-project
    spec:
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - podAffinityTerm:
              labelSelector:
                matchExpressions:
                - key: app
                  operator: In
                  values:
                  - spring-boot-project
              topologyKey: kubernetes.io/hostname
            weight: 100
      containers:
      - env:
        - name: TZ
          value: Asia/Shanghai
        - name: LANG
          value: C.UTF-8
        image: nginx
        imagePullPolicy: IfNotPresent
        lifecycle: {}
        livenessProbe:
          failureThreshold: 2
          initialDelaySeconds: 30
          periodSeconds: 10
          successThreshold: 1
          tcpSocket:
            port: 8761
          timeoutSeconds: 2
        name: spring-boot-project
        ports:
        - containerPort: 8761
          name: web
          protocol: TCP
        readinessProbe:
          failureThreshold: 2
          initialDelaySeconds: 30
          periodSeconds: 10
          successThreshold: 1
          tcpSocket:
            port: 8761
          timeoutSeconds: 2
        resources:
          limits:
            cpu: 994m
            memory: 1170Mi
          requests:
            cpu: 10m
            memory: 55Mi
      dnsPolicy: ClusterFirst
      imagePullSecrets:
      - name: harborkey
      restartPolicy: Always
      securityContext: {}
      serviceAccountName: default
```



运行该配置文件,创建所需资源

```bash
kubectl apply -f spring-boot-project.yaml
```



可选, 直接创建方式

```yaml
kubectl apply -f https://raw.githubusercontent.com/cloudzun/devopslab/main/spring-boot-project.yaml
```



## 创建 Jenkins Piepeline



在 Jenkins 上创建项目，名称为 `spring-boot-project`，类型为 `pipeline`

![image-20221227141905953](readme.assets/image-20221227141905953.png)



使用 http://192.168.14.244/kubernetes/spring-boot-project.git  作为代码库

![image-20221227141925341](readme.assets/image-20221227141925341.png)

注意：因为项目属于公共项目可以不用输入credentials



## 手动触发构建项目



点击 `Build Now` 手动触发构建过程

![image-20221227142145963](readme.assets/image-20221227142145963.png)



第一次触发会可能失败，报错信息为：

```text
The specified working directory should be fully accessible to the remoting executable (RWX): /home/jenkins/agent
```



![image-20221227142231547](readme.assets/image-20221227142231547.png)



解决方法: 在 K8S 节点上设置权限

```bash
chmod -R 777 /opt/workspace
```



再次使用 `Build with Parameter` 创建

![image-20221227142321407](readme.assets/image-20221227142321407.png)



![image-20221227142332488](readme.assets/image-20221227142332488.png)

![image-20221227142400580](readme.assets/image-20221227142400580.png)



完整的 Console Output 的输出如下,可以对照上一节涉及的 Jenkinsfiles 进行过程分析

```bash
Started by user admin
Obtained Jenkinsfile from git http://192.168.14.244/kubernetes/spring-boot-project.git
[Pipeline] Start of Pipeline (hide)
[Pipeline] podTemplate
[Pipeline] {
[Pipeline] node
Created Pod: study-kubernetes default/spring-boot-project-3-748sp-mqvql-14bpd
Agent spring-boot-project-3-748sp-mqvql-14bpd is provisioned from template spring-boot-project_3-748sp-mqvql
---
apiVersion: "v1"
kind: "Pod"
metadata:
  annotations:
    buildUrl: "http://192.168.14.244:8080/job/spring-boot-project/3/"
    runUrl: "job/spring-boot-project/3/"
  labels:
    jenkins: "slave"
    jenkins/label-digest: "651737c27e09c58530bcccf39be7acde2cc59d65"
    jenkins/label: "spring-boot-project_3-748sp"
  name: "spring-boot-project-3-748sp-mqvql-14bpd"
spec:
  containers:
  - args:
    - "$(JENKINS_SECRET)"
    - "$(JENKINS_NAME)"
    env:
    - name: "JENKINS_SECRET"
      value: "********"
    - name: "JENKINS_AGENT_NAME"
      value: "spring-boot-project-3-748sp-mqvql-14bpd"
    - name: "JENKINS_NAME"
      value: "spring-boot-project-3-748sp-mqvql-14bpd"
    - name: "JENKINS_AGENT_WORKDIR"
      value: "/home/jenkins/agent"
    - name: "JENKINS_URL"
      value: "http://192.168.14.244:8080/"
    image: "registry.cn-beijing.aliyuncs.com/citools/jnlp:alpine"
    imagePullPolicy: "IfNotPresent"
    name: "jnlp"
    resources:
      limits: {}
      requests:
        memory: "256Mi"
        cpu: "100m"
    volumeMounts:
    - mountPath: "/etc/localtime"
      name: "localtime"
      readOnly: false
    - mountPath: "/home/jenkins/agent"
      name: "workspace-volume"
      readOnly: false
  - command:
    - "cat"
    env:
    - name: "LANGUAGE"
      value: "en_US:en"
    - name: "LC_ALL"
      value: "en_US.UTF-8"
    - name: "LANG"
      value: "en_US.UTF-8"
    image: "registry.cn-beijing.aliyuncs.com/citools/maven:3.5.3"
    imagePullPolicy: "IfNotPresent"
    name: "build"
    tty: true
    volumeMounts:
    - mountPath: "/etc/localtime"
      name: "localtime"
    - mountPath: "/root/.m2/"
      name: "cachedir"
      readOnly: false
    - mountPath: "/home/jenkins/agent"
      name: "workspace-volume"
      readOnly: false
  - command:
    - "cat"
    env:
    - name: "LANGUAGE"
      value: "en_US:en"
    - name: "LC_ALL"
      value: "en_US.UTF-8"
    - name: "LANG"
      value: "en_US.UTF-8"
    image: "registry.cn-beijing.aliyuncs.com/citools/kubectl:self-1.17"
    imagePullPolicy: "IfNotPresent"
    name: "kubectl"
    tty: true
    volumeMounts:
    - mountPath: "/etc/localtime"
      name: "localtime"
      readOnly: false
    - mountPath: "/home/jenkins/agent"
      name: "workspace-volume"
      readOnly: false
  - command:
    - "cat"
    env:
    - name: "LANGUAGE"
      value: "en_US:en"
    - name: "LC_ALL"
      value: "en_US.UTF-8"
    - name: "LANG"
      value: "en_US.UTF-8"
    image: "registry.cn-beijing.aliyuncs.com/citools/docker:19.03.9-git"
    imagePullPolicy: "IfNotPresent"
    name: "docker"
    tty: true
    volumeMounts:
    - mountPath: "/etc/localtime"
      name: "localtime"
      readOnly: false
    - mountPath: "/var/run/docker.sock"
      name: "dockersock"
      readOnly: false
    - mountPath: "/home/jenkins/agent"
      name: "workspace-volume"
      readOnly: false
  nodeSelector:
    build: "true"
  restartPolicy: "Never"
  securityContext: {}
  volumes:
  - hostPath:
      path: "/usr/share/zoneinfo/Asia/Shanghai"
    name: "localtime"
  - hostPath:
      path: "/opt/m2"
    name: "cachedir"
  - hostPath:
      path: "/var/run/docker.sock"
    name: "dockersock"
  - hostPath:
      path: "/opt/workspace"
    name: "workspace-volume"

Running on spring-boot-project-3-748sp-mqvql-14bpd in /home/jenkins/agent/workspace/spring-boot-project
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Declarative: Checkout SCM)
[Pipeline] checkout
The recommended git tool is: NONE
No credentials specified
Fetching changes from the remote Git repository
 > git rev-parse --resolve-git-dir /home/jenkins/agent/workspace/spring-boot-project/.git # timeout=10
 > git config remote.origin.url http://192.168.14.244/kubernetes/spring-boot-project.git # timeout=10
Fetching upstream changes from http://192.168.14.244/kubernetes/spring-boot-project.git
 > git --version # timeout=10
 > git --version # 'git version 2.20.1'
 > git fetch --tags --force --progress -- http://192.168.14.244/kubernetes/spring-boot-project.git +refs/heads/*:refs/remotes/origin/* # timeout=10
Checking out Revision 8c1ca444666963019de497366676ad4cd16e2d8d (refs/remotes/origin/master)
Commit message: "Update Jenkinsfile"
 > git rev-parse refs/remotes/origin/master^{commit} # timeout=10
 > git config core.sparsecheckout # timeout=10
 > git checkout -f 8c1ca444666963019de497366676ad4cd16e2d8d # timeout=10
 > git rev-list --no-walk b181102f0c645531d7bc8d688a03f1c00bfa8a1d # timeout=10
[Pipeline] }
[Pipeline] // stage
[Pipeline] withEnv
[Pipeline] {
[Pipeline] withEnv
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Pulling Code)
[Pipeline] parallel
[Pipeline] { (Branch: Pulling Code by Jenkins)
[Pipeline] { (Branch: Pulling Code by trigger)
[Pipeline] stage
[Pipeline] { (Pulling Code by Jenkins)
[Pipeline] stage
[Pipeline] { (Pulling Code by trigger)
Stage "Pulling Code by trigger" skipped due to when conditional
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] git
The recommended git tool is: NONE
Warning: CredentialId "gitlab" could not be found.
Fetching changes from the remote Git repository
Checking out Revision 8c1ca444666963019de497366676ad4cd16e2d8d (refs/remotes/origin/master)
Commit message: "Update Jenkinsfile"
[Pipeline] script
[Pipeline] {
[Pipeline] sh
+ git log -n 1 '--pretty=format:%h'
[Pipeline] echo
Current branch is master, Commit ID is 8c1ca44, Image TAG is jenkins-spring-boot-project-3-8c1ca44
[Pipeline] }
[Pipeline] // script
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // parallel
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Building)
 > git rev-parse --resolve-git-dir /home/jenkins/agent/workspace/spring-boot-project/.git # timeout=10
 > git config remote.origin.url http://192.168.14.244/kubernetes/spring-boot-project.git # timeout=10
Fetching upstream changes from http://192.168.14.244/kubernetes/spring-boot-project.git
 > git --version # timeout=10
 > git --version # 'git version 2.20.1'
 > git fetch --tags --force --progress -- http://192.168.14.244/kubernetes/spring-boot-project.git +refs/heads/*:refs/remotes/origin/* # timeout=10
 > git rev-parse refs/remotes/origin/master^{commit} # timeout=10
 > git config core.sparsecheckout # timeout=10
 > git checkout -f 8c1ca444666963019de497366676ad4cd16e2d8d # timeout=10
 > git branch -a -v --no-abbrev # timeout=10
 > git branch -D master # timeout=10
 > git checkout -b master 8c1ca444666963019de497366676ad4cd16e2d8d # timeout=10
[Pipeline] container
[Pipeline] {
[Pipeline] sh
+ curl repo.maven.apache.org
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed

  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
  0     0    0     0    0     0      0      0 --:--:--  0:00:01 --:--:--     0
100   139  100   139    0     0     70      0  0:00:01  0:00:01 --:--:--    70
501 HTTPS Required. 
Use https://repo.maven.apache.org/maven2/
More information at https://links.sonatype.com/central/501-https-required
  + mvn clean install -DskipTests
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------< com.testjava:spring-cloud-eureka >------------------
[INFO] Building spring-cloud-eureka 0.0.1-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-clean-plugin:3.1.0:clean (default-clean) @ spring-cloud-eureka ---
[INFO] Deleting /home/jenkins/agent/workspace/spring-boot-project/target
[INFO] 
[INFO] --- maven-resources-plugin:3.1.0:resources (default-resources) @ spring-cloud-eureka ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource
[INFO] Copying 1 resource
[INFO] 
[INFO] --- maven-compiler-plugin:3.8.1:compile (default-compile) @ spring-cloud-eureka ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to /home/jenkins/agent/workspace/spring-boot-project/target/classes
[INFO] 
[INFO] --- maven-resources-plugin:3.1.0:testResources (default-testResources) @ spring-cloud-eureka ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory /home/jenkins/agent/workspace/spring-boot-project/src/test/resources
[INFO] 
[INFO] --- maven-compiler-plugin:3.8.1:testCompile (default-testCompile) @ spring-cloud-eureka ---
[INFO] No sources to compile
[INFO] 
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ spring-cloud-eureka ---
[INFO] Tests are skipped.
[INFO] 
[INFO] --- maven-jar-plugin:3.1.2:jar (default-jar) @ spring-cloud-eureka ---
[INFO] Building jar: /home/jenkins/agent/workspace/spring-boot-project/target/spring-cloud-eureka-0.0.1-SNAPSHOT.jar
[INFO] 
[INFO] --- spring-boot-maven-plugin:2.1.9.RELEASE:repackage (repackage) @ spring-cloud-eureka ---
[INFO] Replacing main artifact with repackaged archive
[INFO] 
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ spring-cloud-eureka ---
[INFO] Installing /home/jenkins/agent/workspace/spring-boot-project/target/spring-cloud-eureka-0.0.1-SNAPSHOT.jar to /root/.m2/repository/com/testjava/spring-cloud-eureka/0.0.1-SNAPSHOT/spring-cloud-eureka-0.0.1-SNAPSHOT.jar
[INFO] Installing /home/jenkins/agent/workspace/spring-boot-project/pom.xml to /root/.m2/repository/com/testjava/spring-cloud-eureka/0.0.1-SNAPSHOT/spring-cloud-eureka-0.0.1-SNAPSHOT.pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 5.715 s
[INFO] Finished at: 2022-10-06T04:24:56Z
[INFO] ------------------------------------------------------------------------
+ ls target/classes target/generated-sources target/maven-archiver target/maven-status target/spring-cloud-eureka-0.0.1-SNAPSHOT.jar target/spring-cloud-eureka-0.0.1-SNAPSHOT.jar.original
target/spring-cloud-eureka-0.0.1-SNAPSHOT.jar
target/spring-cloud-eureka-0.0.1-SNAPSHOT.jar.original

target/classes:
application.yml
com
logback.xml

target/generated-sources:
annotations

target/maven-archiver:
pom.properties

target/maven-status:
maven-compiler-plugin
[Pipeline] }
[Pipeline] // container
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Docker build for creating image)
[Pipeline] withCredentials
Masking supported pattern matches of $HARBOR_USER or $HARBOR_USER_PSW
[Pipeline] {
[Pipeline] container
[Pipeline] {
[Pipeline] sh
Warning: A secret was passed to "sh" using Groovy String interpolation, which is insecure.
		 Affected argument(s) used the following variable(s): [HARBOR_USER_PSW]
		 See https://jenkins.io/redirect/groovy-string-interpolation for details.
+ echo admin **** jenkins-spring-boot-project-3-8c1ca44
admin **** jenkins-spring-boot-project-3-8c1ca44
+ docker build -t 192.168.14.244:5000/kubernetes/spring-boot-project:jenkins-spring-boot-project-3-8c1ca44 .
Sending build context to Docker daemon  47.36MB

Step 1/3 : FROM registry.cn-beijing.aliyuncs.com/dotbalo/jre:8u211-data
 ---> b1c16eb1456d
Step 2/3 : COPY target/spring-cloud-eureka-0.0.1-SNAPSHOT.jar ./
 ---> f08ef921446d
Step 3/3 : CMD java -jar spring-cloud-eureka-0.0.1-SNAPSHOT.jar
 ---> Running in 981a789849c5
Removing intermediate container 981a789849c5
 ---> 98c1b112bd17
Successfully built 98c1b112bd17
Successfully tagged 192.168.14.244:5000/kubernetes/spring-boot-project:jenkins-spring-boot-project-3-8c1ca44
+ docker login -u admin -p **** 192.168.14.244:5000
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
+ docker push 192.168.14.244:5000/kubernetes/spring-boot-project:jenkins-spring-boot-project-3-8c1ca44
The push refers to repository [192.168.14.244:5000/kubernetes/spring-boot-project]
c7ea2ffdd3f2: Preparing
eff47b948517: Preparing
f25edfe5114d: Preparing
46f3c01a700a: Preparing
37cd0af63488: Preparing
ed211f7d10e3: Preparing
f1b5933fe4b5: Preparing
ed211f7d10e3: Waiting
f1b5933fe4b5: Waiting
eff47b948517: Pushed
f25edfe5114d: Pushed
46f3c01a700a: Pushed
f1b5933fe4b5: Pushed
ed211f7d10e3: Pushed
c7ea2ffdd3f2: Pushed
37cd0af63488: Pushed
jenkins-spring-boot-project-3-8c1ca44: digest: sha256:e3c5e5194397ed7bcbb7626613d27cff20fbd87f1c6e9f8d700c1fb11cfdde8d size: 1785
[Pipeline] }
[Pipeline] // container
[Pipeline] }
[Pipeline] // withCredentials
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Deploying to K8s)
[Pipeline] withCredentials
Masking supported pattern matches of $MY_KUBECONFIG
[Pipeline] {
[Pipeline] container
[Pipeline] {
[Pipeline] sh
Warning: A secret was passed to "sh" using Groovy String interpolation, which is insecure.
		 Affected argument(s) used the following variable(s): [MY_KUBECONFIG]
		 See https://jenkins.io/redirect/groovy-string-interpolation for details.
+ /usr/local/bin/kubectl --kubeconfig **** set image deploy -l 'app=spring-boot-project' 'spring-boot-project=192.168.14.244:5000/kubernetes/spring-boot-project:jenkins-spring-boot-project-3-8c1ca44' -n kubernetes
deployment.apps/spring-boot-project image updated
[Pipeline] }
[Pipeline] // container
[Pipeline] }
[Pipeline] // withCredentials
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // node
[Pipeline] }
[Pipeline] // podTemplate
[Pipeline] End of Pipeline
Finished: SUCCESS
```



## 验证项目构建效果



使用Blue Ocean查看 pipeline 运行过程

![image-20221227143202442](readme.assets/image-20221227143202442.png)



查看 Harbor 映像库

![image-20221227143230018](readme.assets/image-20221227143230018.png)



为应用的服务设置 nodeport 端口

```bash
kubectl patch svc -n kubernetes spring-boot-project  -p '{"spec":{"type": "NodePort"}}'
```



```bash
kubectl patch service spring-boot-project --namespace=kubernetes spring-boot-project --type='json' --patch='[{"op": "replace", "path": "/spec/ports/0/nodePort", "value":30800}]'
```



在浏览器上访问该服务

![image-20221227143511466](readme.assets/image-20221227143511466.png)



![image-20221227143528806](readme.assets/image-20221227143528806.png)



在k8s节点上检查项目对应的工作负载和服务

```bash
kubectl get pod -n kubernetes -o wide
kubectl get svc -n kubernetes -o wide
kubectl get deployment -n kubernetes -o wide
```



```bash
root@node:~# kubectl get pod -n kubernetes -o wide
NAME                                   READY   STATUS    RESTARTS   AGE   IP               NODE   NOMINATED NODE   READINESS GATES
spring-boot-project-545ff8cbf9-xkgmk   1/1     Running   0          76s   10.244.167.153   node   <none>           <none>
root@node:~# kubectl get svc -n kubernetes -o wide
NAME                  TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE   SELECTOR
spring-boot-project   NodePort   10.101.175.61   <none>        8761:30080/TCP   82d   app=spring-boot-project
root@node:~# kubectl get deployment -n kubernetes -o wide
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS            IMAGES                                                                                     SELECTOR
spring-boot-project   1/1     1            1           82d   spring-boot-project   192.168.14.244:5000/kubernetes/spring-boot-project:jenkins-spring-boot-project-4-8c1ca44   app=spring-boot-project
```





 

# 创建自动触发流水线



## 准备 K8S 资源



```bash
kubectl  apply -f https://raw.githubusercontent.com/cloudzun/devopslab/main/go-project.yaml 
kubectl  patch svc -n kubernetes go-project -p  '{"spec":{"type": "NodePort"}}'     
kubectl  patch service go-project --namespace=kubernetes --type='json'  --patch='[{"op": "replace", "path":  "/spec/ports/0/nodePort", "value":30800}]'  
```

 

## 设置 Gitlab



从Admin Settings  Network  页面 (http://192.168.14.244/admin/application_settings/network）

选择 `Allow requests to the local network from web hooks and services`

![图形用户界面, 文本, 应用程序, 电子邮件  描述已自动生成](readme.assets/clip_image090.jpg)

 

从 https://github.com/cloudzun/go-project 导入 go-project 项目

![图形用户界面, 文本, 应用程序, 电子邮件  描述已自动生成](readme.assets/clip_image092.jpg)

 

## 创建 pipeline 项目并配置自动触发



填充代码库路径：http://192.168.14.244/kubernetes/go-project

![图形用户界面, 文本, 应用程序  描述已自动生成](readme.assets/clip_image094.jpg)

 

启用 `Build Triggers`

![img](readme.assets/clip_image096.jpg)

​     记录此处的webhook地址

 

展开高级，创建 `Secret token`

![img](readme.assets/clip_image098.jpg)

​     记录此处的 token

 

在 Gitlab 代码库创建 webhook ( http://192.168.14.244/kubernetes/go-project/-/hooks )

![图形用户界面, 文本, 应用程序, 电子邮件  描述已自动生成](readme.assets/clip_image100.jpg)

 

测试 Hooks

![图形用户界面, 应用程序  描述已自动生成](readme.assets/clip_image102.jpg)

 

![图形用户界面, 应用程序  描述已自动生成](readme.assets/clip_image104.jpg)

 

观察 pipeline 运行情况

![图形用户界面  描述已自动生成](readme.assets/clip_image106.jpg)

 

## 使用 code commit 触发流水线



修改项目库里的代码并提交

![图形用户界面, 文本, 应用程序, 电子邮件  描述已自动生成](readme.assets/clip_image108.jpg)

 

观察 pipeline 运行情况 

![img](readme.assets/clip_image110.jpg)

 

验证服务页面：http://192.168.14.204:30800/api/index.html

![img](readme.assets/clip_image112.jpg)

观察Harbor映像库中的最新映像版本

![img](readme.assets/clip_image114.jpg)



 

# 创建 UAT 和生产环境流水线



## 准备 K8S 资源



```bash
kubectl  create ns uat    
kubectl  apply -f https://raw.githubusercontent.com/cloudzun/devopslab/main/uat-go-project.yaml 
kubectl  patch svc -n uat go-project -p  '{"spec":{"type": "NodePort"}}'    
kubectl  patch service go-project --namespace=uat --type='json'  --patch='[{"op": "replace", "path":  "/spec/ports/0/nodePort", "value":30801}]'  
```

 

## 创建 pipeline 项目



启用 `This project is parameterized`，从下拉菜单中选择 `Image Tag Parameter` 并填充以下属性：

- Name: `IMAGE_TAG`
- Image Name: `kubernetes/go-project`
- Default Tag: `latest`
- Description: `选择需要部署的镜像版本`

![img](readme.assets/clip_image116.jpg)

 然后点击高级，填充 `Registry URL` 和 `Credential ID`



填充 pipeline 脚本

范例见：https://github.com/cloudzun/devopslab/blob/main/UATPipeline

![img](readme.assets/clip_image118.jpg)

 

```yaml
pipeline {
     agent {
    kubernetes {
      cloud 'study-kubernetes'
      slaveConnectTimeout 1200
      yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
    # 只需要配置jnlp和kubectl镜像即可
    - args: [\'$(JENKINS_SECRET)\', \'$(JENKINS_NAME)\']
      image: 'registry.cn-beijing.aliyuncs.com/citools/jnlp:alpine'
      name: jnlp
      imagePullPolicy: IfNotPresent
    - command:
        - "cat"
      env:
        - name: "LANGUAGE"
          value: "en_US:en"
        - name: "LC_ALL"
          value: "en_US.UTF-8"
        - name: "LANG"
          value: "en_US.UTF-8"
      image: "registry.cn-beijing.aliyuncs.com/citools/kubectl:self-1.17"
      imagePullPolicy: "IfNotPresent"
      name: "kubectl"
      tty: true
  restartPolicy: "Never"
'''
    }	
}

   stages {
      stage('Deploy') {
		environment {
			MY_KUBECONFIG = credentials('study-kubernetes')
		}
         steps {
		 container(name: 'kubectl'){
            sh """
               echo ${IMAGE_TAG} # 该变量即为前台选择的镜像
               kubectl --kubeconfig=${MY_KUBECONFIG} set image deployment -l app=${IMAGE_NAME} ${IMAGE_NAME}=${HARBOR_ADDRESS}/${IMAGE_TAG} -n ${NAMESPACE}
            """
         }
		}
      }
   }
   environment {
    HARBOR_ADDRESS = "192.168.14.244:5000"
    NAMESPACE = "uat"
    IMAGE_NAME = "go-project"
    TAG = ""
  }
}
```



## 在 UAT 环境中部署 1.0 版本的服务



使用`Build with Parameters`，并在 IMAGE_TAG 里选择最新版本进行部署

![img](readme.assets/clip_image120.jpg)

 

观察 pipeline运行过程

![图形用户界面, 网站  描述已自动生成](readme.assets/clip_image122.jpg)

 

验证 UAT 页面：http://192.168.14.204:30801/api/index.html

![img](readme.assets/clip_image124.jpg)

## 将开发环境中的代码迭代到 2.0 

在代码库中,更新代码 

![img](readme.assets/clip_image126.jpg)

 

在blue ocean 页面中观察流水线运行情况

![img](readme.assets/clip_image128.jpg)



在镜像库中查看镜像版本信息

![图形用户界面, 文本, 应用程序, 电子邮件  描述已自动生成](readme.assets/clip_image130.jpg)

 

查看新版本应用页面效果

![图形用户界面, 文本, 应用程序, 电子邮件  描述已自动生成](readme.assets/clip_image132.jpg)

 

## 在 UAT 环境中部署 2.0 版本的服务



在 go-project-uat 项目中使用更新的映像进行部署

![图形用户界面, 文本, 应用程序  描述已自动生成](readme.assets/clip_image134.jpg)



 观察流水线运行情况

![img](readme.assets/clip_image136.jpg)

 查看 UAT 版本迭代后的页面

![图形用户界面, 文本, 应用程序, 电子邮件  描述已自动生成](readme.assets/clip_image138.jpg)

