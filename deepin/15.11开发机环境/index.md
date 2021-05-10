[TOC]

> deepin v20 必装软件可以参考 [https://xmuli.tech/posts/db5a13a5/](https://xmuli.tech/posts/db5a13a5/)

# 修改磁盘卷标 

```
sudo umount /dev/sda5
sudo ntfslabel /dev/sda5 D

```

# docker 安装

```
sudo apt-get update

sudo apt-get remove docker docker-engine docker.io

sudo apt-get install -y apt-transport-https ca-certificates curl gnupg2 lsb-release software-properties-common

curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/debian/gpg | sudo apt-key add -

sudo vi /etc/apt/sources.list

sudo apt-get update

sudo apt-get install docker-ce

#用户组设置
sudo gpasswd -a ${USER} docker
sudo groupadd docker

sudo usermod -aG docker $USER

newgrp docker #更新用户组
```

## 设置国内镜像源

```
touch /etc/docker/daemon.json

{
  "registry-mirrors": [
    "https://dockerhub.azk8s.cn",
    "https://hub-mirror.c.163.com"
  ]
}

Docker中国区官方镜像
https://registry.docker-cn.com

网易
http://hub-mirror.c.163.com

ustc
https://docker.mirrors.ustc.edu.cn

中国科技大学
https://docker.mirrors.ustc.edu.cn

阿里云容器 服务
https://cr.console.aliyun.com/

```

```
sudo systemctl daemon-reload
sudo systemctl restart docker
sudo systemctl enable docker
```

- 参考： https://yeasy.gitbooks.io/docker_practice/content/install/debian.html

# docker-composer 安装

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

--- 获取去 https://github.com/docker/compose/releases 下载最新版本 docker-compose-Linux-x86_64 然后执行 `sudo mv docker-compose-Linux-x86_64 /usr/local/bin/docker-compose`

sudo chmod +x /usr/local/bin/docker-compose

docker-compose --version
```
- 参考：https://docs.docker.com/compose/install/

> **国内高速下载**：http://get.daocloud.io/

# phpstorm 安装

- [下载地址](https://www.jetbrains.com/phpstorm/download/download-thanks.html?platform=linux)
- [安装步骤](https://www.cnblogs.com/Dong-Ge/articles/11248689.html)
- 破解包 [http://note.youdao.com/s/CriOwGrx](http://note.youdao.com/s/CriOwGrx)

```
1、File->Setting->Plugins->安装 laravel 以及 laravel Snippets
2、File->Setting->Languages & Frameworks->PHP->Laravel->勾选 Enable Plugin for this project
3、File->Setting->Languages & Frameworks->PHP->Laravel->勾选 Use AutoPopup for completion(usee at own risk eg together with Symfony2)
4、File->Setting->Editor->Code Style->General 的 Line Separator -> 勾选 "Unix and macOS(\n)"
4、File->Setting->Editor->Code Style->PHP-> 勾选 Keep indents on empty lines
```

- php class File Header
```
/**
 * 
 * @author ${USER} <donglf@88.com>
 * @version ${DATE} ${TIME}
 * @copyright ${DATE} www.quanyaotong.com
 * @link https://gitee.com/wsqyt/quanyaotong
 */
```

最后参考 - (https://shimo.im/docs/eavXX1p7YiQMv9oS/read)[https://shimo.im/docs/eavXX1p7YiQMv9oS/read]

# homestead 安装

- https://code.felinae98.cn/linux/deepin%E4%B8%8B%E7%9A%84homestead%E5%AE%89%E8%A3%85/
- https://app.vagrantup.com/laravel/boxes/homestead/versions/9.3.0
- https://www.virtualbox.org/wiki/Linux_Downloads 


# composer docker镜像制作

- 下载最新稳定版 [composer.phar](https://getcomposer.org/download/1.10.1/composer.phar)

- 再制作镜像， Dockfile

```
FROM donglf681/php:7.2-cli-alpine3.11

LABEL maintainer="donglifan <dongggefor2018@163.com>"

# repositorie mirror、timezone
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/' /etc/apk/repositories ; \
    apk add --no-cache --virtual .timezone-deps tzdata; \
    ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime ; \
    echo 'Asia/Shanghai' > /etc/timezone; \
    rm -rf /var/cache/apk/* /tmp/* /var/tmp/* /var/lib/apt/lists/*

ENV COMPOSER_REPO_PACKAGIST "https://mirrors.aliyun.com/composer/"
ENV COMPOSER_ALLOW_SUPERUSER 1
ENV COMPOSER_HOME /tmp
ENV COMPOSER_VERSION 1.10.1

RUN set -ex ; \
	apk add --no-cache --virtual .build-deps \
	    $PHPIZE_DEPS \
	    bzip2-dev; \
	docker-php-ext-install bz2; \
	runDeps="$( \
		scanelf --needed --nobanner --format '%n#p' --recursive /usr/local/lib/php/extensions \
			| tr ',' '\n' \
			| sort -u \
			| awk 'system("[ -e /usr/local/lib/" $1 " ]") == 0 { next } { print "so:" $1 }' \
	)"; \
	apk add --virtual .qyt-phpexts-rundeps $runDeps; \
	apk del .build-deps; \
	rm -rf /var/cache/apk/* /tmp/* /var/tmp/* /var/lib/apt/lists/* /var/www/html

COPY composer.phar /tmp/

# Composer
RUN mv /tmp/composer.phar /usr/local/bin/composer \
	&& chmod +x /usr/local/bin/composer \
    && composer self-update --clean-backups \
	&& composer --ansi --version --no-interaction \
    && composer config -g repo.packagist composer ${COMPOSER_REPO_PACKAGIST}

WORKDIR /app

ENTRYPOINT ["composer"]
```

- 然后复制以下命令，存成可执行文件
```
#!/bin/bash
    tty=
    tty -s && tty=--tty
    docker run \
        $tty \
        --interactive \
        --rm \
        --net=host \
        --user $(id -u):$(id -g) \
        --volume /etc/passwd:/etc/passwd:ro \
        --volume /etc/group:/etc/group:ro \
        --volume /tmp:/tmp \
        --volume $(pwd):/app \
        trrtly/composer:1.9.3 "$@"
```

- 复制到/usr/local/bin/

```
sudo cp composer /usr/local/bin/composer
```
- composer源
```
composer config -gl

composer config -g repo.packagist composer https://packagist.phpcomposer.com

composer config -g repo.packagist composer https://mirrors.aliyun.com/composer
# 安装phpcs
composer global require "squizlabs/php_codesniffer=*"
```

# phpcs 制作

- Dockerfile

```
FROM php:7.2-cli-alpine3.11

LABEL maintainer="donglifan <donggefor2018@163.com>"

ARG PHPCS_VERSION=3.3.2

# repositorie mirror、timezone
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/' /etc/apk/repositories ; \
    apk add --no-cache --virtual .timezone-deps tzdata; \
    ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime ; \
    echo 'Asia/Shanghai' > /etc/timezone; \
    rm -rf /var/cache/apk/* /tmp/* /var/tmp/* /var/lib/apt/lists/*

RUN curl -L https://github.com/squizlabs/PHP_CodeSniffer/releases/download/$PHPCS_VERSION/phpcs.phar > /usr/local/bin/phpcs \
    && chmod +x /usr/local/bin/phpcs \
    && rm -rf /var/cache/apk/* /var/tmp/* /tmp/*

WORKDIR /tmp

```

- docker build -t phpcs .
- 创建 phpcs.sh
- 使用docker compose 启动容器 phpcs, ==默认情况下容器已有`/var/www`目录, 如需要自定义需要挂载工作区目录==(我本地使用)

```
#!/bin/bash
docker exec -i phpcs phpcs "$@"
```

- 拷贝到/usr/local/bin/phpcs

```
sudo cp php.sh /usr/local/bin/phpcs
```

# 安装 Postman

- [下载地址](https://dl.pstmn.io/download/latest/linux64)
- 安装步骤

```
sudo tar -xzvf Postman-linux-x64-7.30.1.tar.gz
sudo mv Postman /opt/
sudo ln -s /opt/Postman/Postman /usr/local/bin/postman

cd /usr/share/applications

sudo vim Postman.desktop

[Desktop Entry]
Type=Application
Comment=postman
Name=Postman
Icon=postman
Exec=/opt/Postman//Postman
Terminal=false
Categories=Application;IDE;
```

# virtualbox 安装 win7 虚拟机

- 安装 virtualbox, 最新 Debian 9 的 deb 包 [下载地址](https://download.virtualbox.org/virtualbox/6.0.18/virtualbox-6.0_6.0.18-136238~Debian~stretch_amd64.deb)
- 下载带有 PE 功能的 win7 iso 镜像

```
sudo dpkg -i virtualbox-6.0_6.0.18-136238~Debian~stretch_amd64.deb
```

- 安装 win7 虚拟机,创建虚拟机，都按默认来创建，步骤如下

```
1、系统-》主板-》启动顺序-》只选择光驱和硬盘，光驱在上，硬盘在下；
2、存储-》选择 iso 镜像
3、设置共享文件夹
4、启动进入 PE 系统，先格式化分区，再恢复系统到C盘
5、安装系统
5、结束
```

# deepin 15.11 Tim 图片无法显示

- 原因是腾讯更新版本，默认请求图片到 ipv6,需要关闭 ipv6

```
sudo sysctl -w net.ipv6.conf.all.disable_ipv6=1

sudo sysctl -w net.ipv6.conf.default.disable_ipv6=1

sudo sysctl -w net.ipv6.conf.lo.disable_ipv6=1

ifconfig
```

# virtualbox 6.0.18 安装 Extension Pack 扩展包

- [对应版本下载](https://www.virtualbox.org/wiki/Download_Old_Builds_6_0)，[我的是](https://download.virtualbox.org/virtualbox/6.0.18/Oracle_VM_VirtualBox_Extension_Pack-6.0.18.vbox-extpack)
- 打开全局设定——扩展，进行安装，如果安装过程出现与gksu-run-helper通信失败的错误，解决方法如下：
> 终端输入：gksu-properties ，将 su 改为 sudo, 重新安装即可

# deepin 15.11 VirtualBox 6.0.18 安装 usb 2.0

- 先安装 virtualBox Extension Pack  扩展包
- 添加当前用户访问 vbox usb 权限

```
$ lsusb

$ whoami

$ sudo usermod -a -G vboxusers dongge

```

- 重启 deepin 系统 即可正常配置
 
# deepin 15.11 安装并破解 XMind Zen

- [破解资源](https://www.ghpym.com/xmindzen.html)，安装包可以去[官网](https://www.xmind.cn/download/)下载
- 安装

```
sudo dpkg -i XMind-ZEN-for-Linux-amd-64bit-10.0.0-201911260056.deb

sudo cp /media/dongge/D/XMind2020_10.1.1.202003312309_Patch_Linux/app.asar /opt/XMind ZEN/resources/
#复制 app.asar 破解版到 /opt/XMind ZEN/resources/ 覆盖
```

# 谷歌浏览器插件下载安装

- [地址](https://crx4chrome.com/crx/149)

  - crxMouse - 鼠标手势
  - Axure RP Extension for Chrome - 原型图 - [zip压缩包下载链接](https://crx.dam.io/files/dogkpdfcklifaemcdfbildhcofnopogp/0.6.3.zip)
  - AdBlock -广告拦截
  - Yet another flags - ip地址

# teamviewer 显示“未就绪，请检查你的连接”

- 解决方法

```
sudo teamviewer --daemon enable
sudo systemctl start teamviewerd.service
```

# 惠普打印机安装

- 打开打印设置软件，搜索网络打印机

![搜索网络打印机](https://note.youdao.com/yws/api/personal/file/WEBf493574ff500f44ffa3e092f47fa5656?method=getImage&version=5728&cstk=KRwPDi7E)

 ==打印机需要勾选启用==
 
- 打印测试页无法打印，需要安装 hp-plugin
 
```
$ hp-plugin // 安装过程会有点慢，并需要 root 密码权限才可安装
```

# `go get` 时会提示错误 `proxyconnect tcp: EOF`

> 本机已安装运行 Qv2ray 使用过系统代理，已配置 goproxy

- 尝试：

  - 使用 ==`go run main.go`== 发现会报 ==`go: github.com/alecthomas/template@v0.0.0-20190718012654-fb15b899a751: Get https://goproxy.cn/github.com/alecthomas/template/@v/v0.0.0-20190718012654-fb15b899a751.mod: proxyconnect tcp: EOF`== 错误
  - 使用 ==`go get github.com/alecthomas/template`== 报 ==`go get github.com/alecthomas/template: module github.com/alecthomas/template: Get https://goproxy.cn/github.com/alecthomas/template/@v/list: proxyconnect tcp: dial tcp 127.0.0.1:8888: connect: connection refused`== 错误
  - `curl https://github.com` 报 `curl: (7) Failed to connect to localhost port 8888: 拒绝连接` 错误
  
- 解决
  
  - 查看本机 8888 端口是否占用 `lsof -i:8888` 发现被占用
  - 查看 env `env|grep -i proxy`
  
    ```
    https_proxy=https://localhost:8888
    http_proxy=http://localhost:8888
    no_proxy=localhost,127.0.0.0/8,::1
    ```
    
    ```
    unset http_proxy
    或：
    unset https_proxy
    ```
# nodejs 和 npm 安装

- 下载[源码包](https://nodejs.org/dist/v12.16.3/node-v12.16.3-linux-x64.tar.xz)
- 安装步骤

```
sudo tar -xvf node-v12.16.3-linux-x64.tar
sudo mv node-v12.16.3-linux-x64 /opt/node/
sudo chmod 777 /opt/node/
sudo ln -s /opt/node/bin/node /usr/local/bin/
node -v
sudo ln -s /opt/node/bin/npm /usr/local/bin/
npm -v
#cnpm
sudo npm install -g cnpm --registry=https://registry.npm.taobao.org
sudo ln -s /opt/node/lib/node_modules/cnpm/bin/cnpm /usr/local/bin/
cnpm -v
```
# 增加 swap 交换区


```
~/swapfile
#查看是否有使用,如果没有输出就是没有使用
$sudo swapon --show

#查看内存使用情况
$free -h -t -s 1

#查看硬盘使用情况
$df -h
```

- 添加交换区

```
mkdir swapfile
cd swapfile
sudo dd if=/dev/zero of=swap bs=1M count=16384
sudo mkswap -f swap
sudo swapon swap
#关闭所有交换区
sudo swapoff swap
```

# 安装最新版 navicat15

- 下载地址 [官方](https://www.navicat.com/en/download/navicat-for-mysql)
- 软件破解

# 安装 ohmyz

- [https://github.com/ohmyzsh/ohmyzsh/wiki/Installing-ZSH](https://github.com/ohmyzsh/ohmyzsh/wiki/Installing-ZSH)
- 安装步骤

```
sudo apt-get install zsh
# gitee 源
wget https://gitee.com/mirrors/oh-my-zsh/raw/master/tools/install.sh


# Default settings
ZSH=${ZSH:-~/.oh-my-zsh}
REPO=${REPO:-ohmyzsh/ohmyzsh}
REMOTE=${REMOTE:-https://github.com/${REPO}.git}
BRANCH=${BRANCH:-master}


# 替换为
REPO=${REPO:-ohmyzsh/ohmyzsh}
REMOTE=${REMOTE:-https://github.com/${REPO}.git}

# 赋予可执行权限
chomd +x install.sh
# 运行
sh install.sh


chsh -s /bin/zsh

```

> 安装过程中会提示是否将 zsh 做为你的默认 shell，确定即可，如果安装完毕默认的还是原生 shell，那么可以使用下面命令将 zsh 设置为默认 shell 

```
chsh -s $(which zsh) #如无效则直接敲 chsh，然后根据提示配置

```
> 参考[https://zc-cris.github.io/archives/7a98167e.html](https://zc-cris.github.io/archives/7a98167e.html)

- phpstorm 使用 zsh: Settings->Tools->Terminal->Shell path->"/usr/bin/zsh",重启后生效


# 上传本地文件到服务器

- [https://bbs.deepin.org/forum.php?mod=viewthread&tid=144230](https://bbs.deepin.org/forum.php?mod=viewthread&tid=144230)




