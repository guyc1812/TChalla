
# Docker

Docker is a tool designed to make it easier to create, deploy, and run applications by using containers.<br>
Containers allow a developer to package up an application with all of the parts it needs, such as libraries and other dependencies, and ship it all out as one package. <br>
By doing so, thanks to the container, the developer can rest assured that the application will run on any other Linux machine regardless of any customized settings that machine might have that could differ from the machine used for writing and testing the code.

[gitbook](https://yeasy.gitbooks.io/docker_practice/content/)


## Docker vs VM


## Basic

`pull`<br>
`docker pull ubuntu(docker pull registry.hub.docker.com/ubuntu:lastest)`

`run`<br>
`docker run ubuntu`<br>
`docker run -d ubuntu /bin/bash -c "echo hello world"`<br>
`-d`  ==>守护态进程(daemonized)<br>
`--rm` ==>容器在终止后会立即删除

`exec`<br>
`docker exec -it ubuntu /bin/bash`<br>
`-i` ==>标准输入<br>
`-t` ==>分配伪终端(pseudo-tty)

`tag`<br>
`docker tag dl.docker.com:5000/ubuntu:lastest ubuntu:lastest`

`search`<br>
`docker search mysql`

`rmi` --remove image<br>
`docker rmi  ubuntu:lastest`  --first remove tag<br>
`docker rmi  dl.docker.com:5000/ubuntu:lastest`  --remove image

`rm` --remove container<br>
`docker rm e81`

`ls`<br>
`docker images`<br>
`docker image ls`

`ps`<br>
`docker ps -a`<br>
`docker ps -a -q`  查看处于终止态的容器

`docker stop ce5`<br>
`docker start ec5`<br>
`docker restart ce5`<br>
`docker logs ce5`<br>

`port`<br>
`docker port influxdb 8086`<br>
==> 127.0.0.1:8086

`docker inspect [容器ID]`<br>
查看容器所有变量值

`save` 导出<br>
`docker save -o ubuntu_14.04.tar ubuntu:14.04`<br>
`load` 导入<br>
`docker load < ubuntu_14.04.tar` or `docker load --input ubuntu_14.04.tar`

## 创建私有仓库

`docker run -d -p 5000:5000 registry`<br>
`docker  run  -d  -p  5000:5000  -v  /opt/data/registry:/tmp/registry  registry`<br>
默认仓库创建在/tmp/registry

创建并上传image<br>
`docker tag ubuntu:14.04  10.64.244.150:5000/ubuntu`<br>
`docker push  10.64.244.150:5000/ubuntu`

查看仓库中镜像<br>
`curl http:// 10.64.244.150:5000/v1/search`

下载镜像<br>
`docker pull  10.64.244.150:5000/ubuntu`

最后再次添加通用标签<br>
`docker tag  10.64.244.150:5000/ubuntu  ubuntu:14.04`

## 数据卷容器

创建数据卷容器container，创建数据卷并挂载到/dbdata<br>
`docker  run  -it  -v  /dbdata  --name  container  ubuntu`<br>
此时 dbdata 文件夹会出现在 container  根目录中

2.使用其他容器挂载  container 容器中的数据卷<br>
`docker  run  -it  --volume-from  container  --name  db1  ubuntu`<br>
`docker  run  -it  --volume-from  container  --name  db2  ubuntu`

此时db1 和db2都挂载同一个数据卷到相同的 /dbdata 目录, <br>
三个容器任意一方在该目录的写入，其他容器都可以看到。

## 利用数据卷迁移数据

备份<br>
`docker  run  --volumes-from  dbdata  -v  ${pwd}:/backup  --name  worker  ubuntu` <br>
`tar  cvf  /backup/backup.tar  /dbdata`<br>
1.创建一个容器 worker <br>
2.使用  `--volumes-from`  来挂在数据卷<br>
3.使用  `-v  ${pwd}:/backup` 来挂在本地目录到worker容器的/backup目录<br>
4.打包  /dbdata 下的内容到  /backup/backup.tar

恢复<br>
1.创建一个带有数据卷的容器dbdata2<br>
`docker run -v  /dbdata  --name  dbdata2  ubuntu  /bin/bash`<br>
2.创建另一个新容器，挂在数据卷容器 dbdata2 ，并解压备份文件到所挂载的容器卷中即可<br>
`docker run  -volumes-from  dbdata2  -v  $(pwd):/backup  busybox  tar  xvf  /backup/backup.tar`

## 端口映射

`docker run  -d  -P  training/webapp  python  app.py`<br>
-P(大写)  Docker会随机映射一个49000-49900的端口到容器内部开放放的网络端口， 0.0.0.0:49155->5000/tcp

绑定本地所有接口上的所有地址<br>
`docker  run  -d  -p  5000:5000  training/webapp  python  app.py`<br>
`docker  run  -d  -p  5000:5000  -p  3000:80  training/webapp  python  app.py`

绑定指定地址的指定端口<br>
`docker  run  -d  -p  127.0.0.1:5000:5000  training/webapp  python  app.py`<br>

绑定指定地址的任意端口，本地主机自动分配端口<br>
`docker  run  -d  -p  127.0.0.1: :5000  training/webapp  python  app.py`

绑定udp端口<br>
`docker  run  -d  -p  127.0.0.1:5000:5000/udp  training/webapp  python  app.py`

## 容器互联

1.创建一个新的数据库容器<br>
`docker  run  -d  --name  db  training/postgres`

2.创建一个新的web容器，并将它链接到db容器<br>
`--link  name: alias`<br>
`docker  run  -d  -P  --name web  --link  db:db  training/webapp  python  app.py`

3.docker通过两种方式为容器公开链接信息<br>
环境变量, 通过env查看<br>
更新 /etc/hosts 文件

## Dockerfile

**FROM**<br>
`FROM <image>`    or    `FROM <image>:<tag>`

**MAINTAINER****
`MAINTAINER <name>`

**RUN**<br>
`RUN <command>`
`RUN [“executable”, “param1”, "param2"]`<br> 
`RUN ["/bin/bash", "-c", "echo hello"]`

**CMD --可被docker run覆盖， 只会执行最后一条**<br>
`CMD [" executable ", " param1 ", " param2 "]` 指定exec执行<br>
`CMD command param1 param2 ` 在 /bin/bash 中执行，提供给需要交互的应用<br>
`CMD ["param1", "param2"]` 提供给ENTRYPOINT的默认参数<br>
每个dockerfile只能有一种CMD命令，如果多条则只有最后一条会执行

**EXPOSE**<br>
`EXPOSE  22  80  8443`

**ENV**<br>
`ENV <key> <value>`<br>
会被后续RUN指令使用，并在容器运行时保持

**ADD**<br>
`ADD <src> <dest>`<br>
src可以为Dockerfile 所在目录的相对路径；一个URL；一个tar文件(会自动解压为目录)<br>
dest为容器中路径

**COPY**<br>
`COPY  <src> <dest>`<br>
src为 Dockerfile 所在目录的相对路径，文件或目录。<br>
dest为 容器中路径，目录不存在时会自动创建

**ENTRYPOINT  --不可被docker run覆盖，只会执行最后一条**<br>
`ENTRYPOINT  [“executable”, “param1”, "param2"]`<br>
`ENTRYPOINT  command param1 param2`

**VOLUME**<br>
`VOLUME ["/data"]`  挂载

**USER**<br>
`USER daemon`<br>
制定运行容器时的用户名或UID，后续的RUN也会指定用户

**WORKDIR**<br>
`WORKDIR /a`<br>
`WORKDIR b`<br>
`WORKDIR c`<br>
为后续的RUN, CMD, ENTRYPOINT指令配置工作目录：/a/b/c

**ONBUILD**<br>
若本image作为其他镜像的基础镜像时<br>
会先执行ONBUILD指定的指令

(image-A)<br>
`ONBUILD ADD ./app/src`<br>
`ONBUILD RUN /usr/local/bin/python-build  --dir  /app/src`

`FROM   image-A`<br>
`#Automatically run the following`<br>
`ADD ./app/src`<br>
`RUN /usr/local/bin/python-build  --dir  /app/src`

使用 ONBUILD指令的镜像，推荐在标签中注明，例如ruby:1.9-onbuild

## Docker Compose

1.Install<br>
curl -L https://github.com/docker/compose/releases/download/1.14.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

2.Apply executable permissions to the binary:<br>
`sudo chmod +x /usr/local/bin/docker-compose`

3.Test the installation.<br>

`$ docker-compose --version`<br>
`docker-compose version 1.14.0, build 1719ceb`

`docker-compose up -d`<br>
`docker-compose stop`

`docker-compose rm -f`<br>
`docker-compose restart`

使用nginx-docker须使用绝对IP地址<br>
$host,$server_name,localhost都是无效的

​