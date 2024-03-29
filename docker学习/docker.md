## docker

---

1. 为什么使用docker

> 不知道怎么去解释吧，感觉就是省去了很多环境的安装，比如你要在服务器运行一个node服务，你必须要使用pm2这样的工具去启用，然后假如还有个python的应用，我想肯定还要下个python的工具吧，那么这样就很多很杂，在自己的linux主机就变的很复杂很不好管理，而docker可以完美的去解决这些场景，不需要pm2也能在服务端跑起一个node服务；

2. docker的概念

- image: 镜像，类似于一个说明书，告诉docker怎么去做出一个这样的应用出来；

- container： 容器，根据这个image生成，生成以后就可以通过命令去使用；

3. docker常用的指令

- docker image ls 					<查看镜像列表>
- docker run -d -p 3000:8000  列表名  	<将镜像8000运行在本机的3000端口后台运行，产生一个容器>
- docker ps -a 						<查看容器的执行情况>
- docker build -t 镜像名称 .			<创建一个镜像，点标示文件的文件位置>
- docker container stop 容器id			<暂停一个服务>

4. 如何去创建一个简易的服务（以官网的例子说明，node更简单了）

- 新建一个空目录，mkdir docker_example ，名字随意；
- cd docker_example目录， 然后首先创建三个文件夹, 
touch Dockerfile，touch app.py,touch requirements.txt;

>  Dockerfile: 这个文件夹的名字必须是这个，然后说下他的配置；我以官网的例子然后看注释：

```
Dockerfile

FROM 		node     	  	<继成>
COpy 		app/app  	  	<拷贝>
WORKDIR 	/app  	  		<工作目录>
RUN 		npm install   	<执行命令，在编译阶段执行>
EXPOSE 		3000       		<暴露一个端口>
CMD 		node app.js		<命令，容器运行阶段的命令>
```

```
# 使用python作为运行时候的夫镜像，意思这个应用继承自python
FROM python:2.7-slim

# 创建一个工作目录叫做app
WORKDIR /app

# 将当前目录内容复制到/ app的容器中
COPY . /app

# 安装requirements.txt中指定的任何所需包（这里不需要关心pip的问题）
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# 使端口80可用于此容器外的世界，我们在本机随意一个接口去对接这个接口即可
EXPOSE 80

# 定义一个环境变量
ENV NAME World

# 在容器启动时运行app.py，类似于我们在命令行去 python app.py(类似于node app.js)
CMD ["python", "app.py"]

```
>  app.py 这个就是服务代码了，看不懂没关系，把它当作js看就是了。。。

```
from flask import Flask
from redis import Redis, RedisError
import os
import socket

# Connect to Redis
redis = Redis(host="redis", db=0, socket_connect_timeout=2, socket_timeout=2)

app = Flask(__name__)

@app.route("/")
def hello():
    try:
        visits = redis.incr("counter")
    except RedisError:
        visits = "<i>cannot connect to Redis, counter disabled</i>"

    html = "<h3>Hello {name}!</h3>" \
           "<b>Hostname:</b> {hostname}<br/>" \
           "<b>Visits:</b> {visits}"
    return html.format(name=os.getenv("NAME", "world"), hostname=socket.gethostname(), visits=visits)

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```
> requirements.txt,这就类似于node里面package.json文件了

```
Flask
Redis
```
- 三个文件创建以后，那我们就去把这个镜像生成，docker build --tag=friendlyhello .

> --tag也可以写成--t,指定版本，记住一定要在这个目录下面执行这个命令，镜像名字为friendlyhello，好像不能有大写

- 执行 docker image ls；就可以看到你刚刚的friendlyhello这个镜像了，

- 执行 docker run -p 4000:80 friendlyhello，然后用浏览器访问云主机的4000端口，然后就可以看到欢迎页了，是不是很简单，很神奇，也不需要配置一大堆的东西了；


----
初步的一个简单基于docker的服务就跑通了，后期在慢慢研究它更多的功能吧；

