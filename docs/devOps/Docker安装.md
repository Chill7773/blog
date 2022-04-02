[TOC]

## 安装（Ubuntu）

1. 首先更新 apt 软件包数据库，以确保软件包列表是最新的：

```shell
sudo apt-get update
```

2. 安装一些软件包，以允许 apt 通过 HTTPS 使用存储库：

```shell
sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
```

3. 首先，我们添加阿里云提供的镜像源以便于加快国内安装速度，先添加相应的密钥：

```shell
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
```

4. 再添加相应源的信息(设置存储库)：

```shell
sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
```

5. 安装

```shell
# 更新 apt 索引库
sudo apt-get update
# 安装 docker-ce
sudo apt-get install docker-ce
```

## 使用阿里云镜像源

国内拉取 Docker Hub 的速度非常慢，好在阿里云提供了镜像加速器。

首先，我们需要编辑 /etc/docker/daemon.json 文件：

```shell
sudo vim /etc/docker/daemon.json
```

然后加入如下内容=：

```json
{
  "registry-mirrors": ["https://n6syp70m.mirror.aliyuncs.com"]
}
```

修改之后，需要重启 Docker 服务，让修改生效。使用如下命令：

```shell
sudo service docker restart
```
