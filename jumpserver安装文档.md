# 安装文档

**Jumpserver 环境要求：**

- 硬件配置: 2个CPU核心, 4G 内存, 50G 硬盘（最低）
- 操作系统: Linux 发行版 x86_64
- Python = 3.6.x
- Mysql Server ≥ 5.6
- Mariadb Server ≥ 5.5.56
- Redis

生产环境部署建议部署 [1.4.8 版本](http://docs.jumpserver.org/zh/1.4.8/step_by_step.html)

- [极速安装](https://docs.jumpserver.org/zh/docs/setup_by_fast.html)
- [Docker 安装](https://docs.jumpserver.org/zh/docs/dockerinstall.html)
- [一站式、分布式安装文档](https://docs.jumpserver.org/zh/docs/setup_by_prod.html)
- [更新升级](https://docs.jumpserver.org/zh/docs/upgrade.html)
- [服务迁移](https://docs.jumpserver.org/zh/docs/migration.html)
- [卸载文档](https://docs.jumpserver.org/zh/docs/uninstall.html)

**安装步骤**

1. 安装 python3.6 mysql Redis

```shell
# 编译或者直接从仓库获取都可以, 版本要求参考最上方环境要求
```

1. 创建 py3 虚拟环境

```shell
$ python3.6 -m venv /opt/py3
```

1. 载入 py3 虚拟环境

```shell
# 每次操作 jumpserver 都需要使用下面的命令载入 py3 虚拟环境
$ source /opt/py3/bin/activate
# 部分系统可能会提示 source: not found , 可以使用 "." 代替 "source"
$ . /opt/py3/bin/activate
```

1. 获取 jumpserver 代码

```shell
$ cd /opt
$ git clone --depth=1 https://github.com/jumpserver/jumpserver.git
# 如果没有安装 git 请先安装
```

1. 安装依赖

```shell
$ cd /opt/jumpserver/requirements
# 根据当前系统, 选择对应的文件执行即可
# 如 Centos: yum install -y $(cat rpm_requirements.txt)
# 如 Ubuntu: apt-get install -y $(cat deb_requirements.txt)

$ pip install -r requirements.txt
# 确保已经载入 py3 虚拟环境, 中间如果遇到报错一般是依赖包没装全, 可以通过 搜索引擎 解决
```

1. 修改配置文件

```shell
$ cd /opt/jumpserver
$ cp config_example.yml config.yml
$ vim config.yml
# 注意 SECRET_KEY 和 BOOTSTRAP_TOKEN 不能使用纯数字字符串
```

1. 启动 jumpserver

```shell
$ cd /opt/jumpserver
$ ./jms start  # 可以 -d 参数在后台运行 ./jms start -d
# 确保已经载入 py3 虚拟环境, 中间如果遇到报错请参考 FAQ 文档或者 搜索引擎 解决
```

1. 正常部署 koko 组件

```shell
# 注意, coco 目前已经被 koko 取代

$ cd /opt

# 访问 https://github.com/jumpserver/koko/releases 下载对应 release 包并解压到 /opt目录
$ wget https://github.com/jumpserver/koko/releases/download/1.5.2/koko-master-6d4e69b-linux-amd64.tar.gz

$ tar xf koko-master-6d4e69b-linux-amd64.tar.gz

$ chown -R root:root kokodir
$ cd kokodir

$ cp config_example.yml config.yml
$ vim config.yml
# BOOTSTRAP_TOKEN 需要从 jumpserver/config.yml 里面获取, 保证一致
$ ./koko
```

8.1. docker 部署 koko 组件

```shell
# 如果前面已经部署了 coco, 可以跳过部署 koko

$ docker run --name jms_koko -d -p 2222:2222 -p 127.0.0.1:5000:5000 -e CORE_HOST=http://<Jumpserver_url> -e BOOTSTRAP_TOKEN=<Jumpserver_BOOTSTRAP_TOKEN> --restart=always jumpserver/jms_koko:<Tag>
# <Jumpserver_url> 为 jumpserver 的 url 地址, <Jumpserver_BOOTSTRAP_TOKEN> 需要从 jumpserver/config.yml 里面获取, 保证一致, <Tag> 是版本
# 例: docker run --name jms_koko -d -p 2222:2222 -p 127.0.0.1:5000:5000 -e CORE_HOST=http://192.168.244.144:8080 -e BOOTSTRAP_TOKEN=abcdefg1234 --restart=always jumpserver/jms_koko:1.5.2
```

1. 正常部署 coco 组件

```shell
# 注意, coco 目前已经被 koko 取代, 你任可以在当前版本中使用 coco, 或者跳过此步骤直接部署 koko

$ cd /opt
$ git clone --depth=1 https://github.com/jumpserver/coco.git

$ cd /opt/coco/requirements
# 根据当前系统, 选择对应的文件执行即可

$ pip install -r requirements.txt
# 确保已经载入 py3 虚拟环境, 中间如果遇到报错一般是依赖包没装全, 可以通过 搜索引擎 解决

$ cd /opt/coco
$ cp config_example.yml config.yml
$ vim config.yml
# BOOTSTRAP_TOKEN 需要从 jumpserver/config.yml 里面获取, 保证一致

$ ./cocod start  # 可以 -d 参数在后台运行 ./cocod start -d
# 确保已经载入 py3 虚拟环境, 中间如果遇到报错请参考 FAQ 文档或者 搜索引擎 解决
```

9.1 docker 部署 coco 组件

```shell
# 注意, coco 目前已经被 koko 取代, 你任可以在当前版本中使用 coco

# 如果正常部署 coco 太麻烦, 你可以直接使用 docker 部署, docker 请使用最新版本
$ docker run --name jms_coco -d -p 2222:2222 -p 5000:5000 -e CORE_HOST=http://<Jumpserver_url> -e BOOTSTRAP_TOKEN=<Jumpserver_BOOTSTRAP_TOKEN> --restart=always jumpserver/jms_coco:<Tag>
# <Jumpserver_url> 为 jumpserver 的 url 地址, <Jumpserver_BOOTSTRAP_TOKEN> 需要从 jumpserver/config.yml 里面获取, 保证一致, <Tag> 是版本
# 例: docker run --name jms_coco -d -p 2222:2222 -p 5000:5000 -e CORE_HOST=http://192.168.244.144:8080 -e BOOTSTRAP_TOKEN=abcdefg1234 --restart=always jumpserver/jms_coco:1.5.2
```

1. 正常安装并启动 guacamole 组件

```shell
# 建议使用 docker 部署 guacamole 组件 , 部分环境可能无法正常编译安装

$ cd /opt
$ git clone --depth=1 https://github.com/jumpserver/docker-guacamole.git
$ cd /opt/docker-guacamole
$ tar xf guacamole-server-1.0.0.tar.gz
$ cd /opt/docker-guacamole/guacamole-server-1.0.0

# 根据 http://guacamole.apache.org/doc/gug/installing-guacamole.html 文档安装对应的依赖包

# Ubuntu: apt-get install -y libcairo2-dev libjpeg-turbo8-dev libpng12-dev libossp-uuid-dev
# Ubuntu: apt-get install -y libavcodec-dev libavutil-dev libswscale-dev libfreerdp-dev libpango1.0-dev libssh2-1-dev libtelnet-dev libvncserver-dev libpulse-dev libssl-dev libvorbis-dev libwebp-dev
# ln -s /usr/local/lib/freerdp/*.so /usr/lib/x86_64-linux-gnu/freerdp

# Debian: apt-get install -y libcairo2-dev libjpeg62-turbo-dev libpng12-dev libossp-uuid-dev
# Debian: apt-get install -y libavcodec-dev libavutil-dev libswscale-dev libfreerdp-dev libpango1.0-dev libssh2-1-dev libtelnet-dev libvncserver-dev libpulse-dev libssl-dev libvorbis-dev libwebp-dev
# ln -s /usr/local/lib/freerdp/*.so /usr/lib/x86_64-linux-gnu/freerdp

# Fedora/CentOS/RHEL: yum install -y cairo-devel libjpeg-turbo-devel libpng-devel uuid-devel
# Fedora/CentOS/RHEL: yum install -y ffmpeg-devel freerdp-devel pango-devel libssh2-devel libtelnet-devel libvncserver-devel pulseaudio-libs-devel openssl-devel libvorbis-devel libwebp-devel
# ln -s /usr/local/lib/freerdp/*.so /usr/lib64/freerdp/

$ autoreconf -fi
$ ./configure --with-init-dir=/etc/init.d
$ make
$ make install

# 先在当前环境配置好 jdk8 jre8
# Ubuntu: apt-get -y install default-jre default-jdk
# Centos: yum install -y java-1.8.0-openjdk

# 访问 https://tomcat.apache.org/download-90.cgi 下载最新的 tomcat9
$ mkdir -p /config/guacamole /config/guacamole/lib /config/guacamole/extensions /config/guacamole/data/log/
$ cd /config
$ wget http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-9/v9.0.22/bin/apache-tomcat-9.0.22.tar.gz
$ tar xf apache-tomcat-9.0.22.tar.gz
$ mv apache-tomcat-9.0.22 tomcat9
$ rm -rf /config/tomcat9/webapps/*
$ sed -i 's/Connector port="8080"/Connector port="8081"/g' /config/tomcat9/conf/server.xml
$ echo "java.util.logging.ConsoleHandler.encoding = UTF-8" >> /config/tomcat9/conf/logging.properties
$ ln -sf /opt/docker-guacamole/guacamole-1.0.0.war /config/tomcat9/webapps/ROOT.war
$ ln -sf /opt/docker-guacamole/guacamole-auth-jumpserver-1.0.0.jar /config/guacamole/extensions/guacamole-auth-jumpserver-1.0.0.jar
$ ln -sf /opt/docker-guacamole/root/app/guacamole/guacamole.properties /config/guacamole/guacamole.properties
$ wget https://github.com/ibuler/ssh-forward/releases/download/v0.0.5/linux-amd64.tar.gz
$ tar xf linux-amd64.tar.gz -C /bin/
$ chmod +x /bin/ssh-forward

# 设置 guacamole 环境
$ export JUMPSERVER_SERVER=http://127.0.0.1:8080  # http://127.0.0.1:8080 指 jumpserver 访问地址
$ echo "export JUMPSERVER_SERVER=http://127.0.0.1:8080" >> ~/.bashrc

# BOOTSTRAP_TOKEN 为 Jumpserver/config.yml 里面的 BOOTSTRAP_TOKEN 值
$ export BOOTSTRAP_TOKEN=******
$ echo "export BOOTSTRAP_TOKEN=******" >> ~/.bashrc
$ export JUMPSERVER_KEY_DIR=/config/guacamole/keys
$ echo "export JUMPSERVER_KEY_DIR=/config/guacamole/keys" >> ~/.bashrc
$ export GUACAMOLE_HOME=/config/guacamole
$ echo "export GUACAMOLE_HOME=/config/guacamole" >> ~/.bashrc

$ /etc/init.d/guacd start
$ sh /config/tomcat9/bin/startup.sh
```

10.1 docker 部署 guacamole 组件

```shell
$ docker run --name jms_guacamole -d -p 127.0.0.1:8081:8081 -e JUMPSERVER_SERVER=http://<Jumpserver_url> -e BOOTSTRAP_TOKEN=<Jumpserver_BOOTSTRAP_TOKEN> jumpserver/jms_guacamole:<Tag>
# <Jumpserver_url> 为 jumpserver 的 url 地址, <Jumpserver_BOOTSTRAP_TOKEN> 需要从 jumpserver/config.yml 里面获取, 保证一致, <Tag> 是版本
# 例: docker run --name jms_guacamole -d -p 127.0.0.1:8081:8081 -e JUMPSERVER_SERVER=http://192.168.244.144:8080 -e BOOTSTRAP_TOKEN=abcdefg1234 jumpserver/jms_guacamole:1.5.2
```

1. 下载 luna 组件

```shell
$ cd /opt

# 访问 https://github.com/jumpserver/luna/releases 获取
$ wget https://github.com/jumpserver/luna/releases/download/1.5.2/luna.tar.gz

$ tar xf luna.tar.gz
$ chown -R root:root luna
```

1. 配置 nginx 整合各组件

```shell
# 参考 http://nginx.org/en/linux_packages.html 文档安装最新的稳定版 nginx

$ rm -rf /etc/nginx/conf.d/default.conf
$ vim /etc/nginx/conf.d/jumpserver.conf

server {
    listen 80;

    client_max_body_size 100m;  # 录像及文件上传大小限制

    location /luna/ {
        try_files $uri / /index.html;
        alias /opt/luna/;  # luna 路径, 如果修改安装目录, 此处需要修改
    }

    location /media/ {
        add_header Content-Encoding gzip;
        root /opt/jumpserver/data/;  # 录像位置, 如果修改安装目录, 此处需要修改
    }

    location /static/ {
        root /opt/jumpserver/data/;  # 静态资源, 如果修改安装目录, 此处需要修改
    }

    location /socket.io/ {
        proxy_pass       http://localhost:5000/socket.io/;
        proxy_buffering off;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        access_log off;
    }

    location /coco/ {
        proxy_pass       http://localhost:5000/coco/;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        access_log off;
    }

    location /guacamole/ {
        proxy_pass       http://localhost:8081/;
        proxy_buffering off;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $http_connection;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        access_log off;
    }

    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
$ nginx -t
$ nginx -s reload
```

1. 开始使用 Jumpserver

```
# 检查应用是否已经正常运行
# 服务全部启动后, 访问 jumpserver 服务器 nginx 代理的 80 端口, 不要通过8080端口访问
# 默认账号: admin 密码: admin
```

