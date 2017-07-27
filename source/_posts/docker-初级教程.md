---
title: docker 初级教程
date: 2016-03-26 15:38:21
tags: docker
---

hub.docker.com的注册以及容器镜像的操作
<!--more-->

##注册hub.docker.com

点击[https://hub.docker.com/register](https://hub.docker.com/register/)进入注册的页面

![屏幕快照 2016-04-20 上午10.30.55](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-20-屏幕快照 2016-04-20 上午10.30.55.png)

当输入相应的用户名 邮箱和密码之后,docker会发送一封邮件到您的邮箱当中 。

![屏幕快照 2016-04-20 上午10.33.42](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-20-屏幕快照 2016-04-20 上午10.33.42.png)

点击“confirm your Email"之后就完成了相应的注册

浏览打开[https://hub.docker.com/login/](https://hub.docker.com/login/)输入注册时相应的用户名和密码，并且进行登录 

![屏幕快照 2016-04-20 上午10.34.55](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-20-屏幕快照 2016-04-20 上午10.34.55.png)

最终的界面 

![屏幕快照 2016-04-20 上午10.35.35](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-20-屏幕快照 2016-04-20 上午10.35.35.png)

## 安装DockerToolbox工具

我们进入[https://www.docker.com/products/docker-toolbox](https://www.docker.com/products/docker-toolbox),根据系统的不同来下载相应的软件

![屏幕快照 2016-04-21 上午11.16.27](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.16.27.png)

接下来我们打开所下载下来的安装文件，会进入下面的界面

![屏幕快照 2016-04-21 上午11.17.57](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.17.57.png)

默认点击继续

![屏幕快照 2016-04-21 上午11.18.16](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.18.16.png)

默认点击继续

![屏幕快照 2016-04-21 上午11.18.27](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.18.27.png)

到达如上图所示的界面时我们点击**Docker Quickstart Terminal**，并可以启动一个Docker容器

![屏幕快照 2016-04-21 上午11.18.57](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.18.57.png)

启动完成之后会进入下面的界面

![屏幕快照 2016-04-21 上午11.20.02](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.20.02.png)

下图所示的是我们安装的docker容器的版本信息

![屏幕快照 2016-04-21 上午11.20.18](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.20.18.png)

	
##运行一个容器
我们使用docker images 可以查看当前所有的镜像，因为是初装的原因没有任何的镜像 

![屏幕快照 2016-04-21 上午11.26.26](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.26.26.png)

这里我们需要安装一个镜像 ，比如我们需要安装一个Ubuntu的镜像，我们仅需要通过pull ubuntu的命令即可,下面的界面是docker正在获取镜像。

![屏幕快照 2016-04-21 上午11.28.28](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.28.28.png)

当获取完成之后，我们可以通过使用**docker images**命令来获取当前的所有的镜像,下面的ubuntu并是我们 刚才获取到的镜像。
镜像是静态的形式，我们将其运行起来，运行的镜像又被称做**容器**。

![屏幕快照 2016-04-21 上午11.31.32](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.31.32.png)
采用run的命令来运行一个容器

>docker run -it ubuntu

这里我们可以看到用户名已经变成root了，这表示我们已经进入了容器的内部

![屏幕快照 2016-04-21 上午11.34.40](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.34.40.png)

容器是单独隔离的，你在其中做任何的操作都不会影响到原来的系统。

例如：对其进行安装一个nginx的服务器

>sudo apt-get install -y nginx

完成后我们执行nginx -v 会返现nginx已经安装完成 

![屏幕快照 2016-04-21 上午11.37.26](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.37.26.png)

##将容器转化为镜像

在上一个环境我们已经在容器当中 安装了一个nginx，**容器是一个运行时的环境，一旦退出当前所有的操作都会丢失。**这里我们需要将其转换成一个镜像。

我们在刚才运行的终端当中调用 exit来退出容器

![屏幕快照 2016-04-21 上午11.39.43](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.39.43.png)

每个窗口都会有一个ID，通过这个ID来辨识不同的容器对象，也是我们将其操作的标识。通过调用 一个ps命令可以查看当中运行的容器。附带一个-a表示曾经运行过的容器。

![屏幕快照 2016-04-21 上午11.42.15](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.42.15.png)

commit是将容器转换成镜像的命令。通过下列的命令我们将容器转换成一个镜像

![屏幕快照 2016-04-21 上午11.44.10](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.44.10.png)

其中-m 参数用于提交时的备注信息，-a是指定用户信息；f76e0ef497c9代表的是容器的ID；wenchangshou/sta†ic_web:v1 指定目标镜像的用户名、仓库名和tag信息。

创建成功后会返回这个镜像的ID。其中的wenchangshou需要换成你自己注册时的用户名


通过调用docker images，可以看出多了一个wenchangshou/static_web的镜像

![屏幕快照 2016-04-21 上午11.46.41](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.46.41.png)

我们运行**docker run -it wenchangshou/static_web 就会运行一个已经安装好nginx的容器

![屏幕快照 2016-04-21 上午11.48.24](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.48.24.png)

##提交镜像到Docker Hub

这里我们需要将刚才所创建的镜像上传到[https://hub.docker.com/](https://hub.docker.com/)

在操作之前我们需要在终端里面登录 

> docker login

输出上面的命令之后终端会要求我们输入相应的Username、password、email，成功之后会提示Login Seccess

![屏幕快照 2016-04-21 上午11.53.22](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.53.22.png)

这时我们需刚刚的创建的镜像推送到hub.docker当中，我们使用下面的命令

>docker push 

上传成功之后会输出下列的信息

![屏幕快照 2016-04-21 上午11.56.16](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.56.16.png)

这里我们进入 hub.docker的网站发现刚才所推荐的镜像已经推送成功。

![屏幕快照 2016-04-21 上午11.57.05](http://7xrkms.com1.z0.glb.clouddn.com/2016-04-21-屏幕快照 2016-04-21 上午11.57.05.png)

推送成功之后，我们在其他的电脑当中使用下列的命令，就会一键接收一个已经安装nginx的镜像。

>docker pull wenchangshou/static_web