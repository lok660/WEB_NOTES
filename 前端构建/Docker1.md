

### 前端构建(SPA)

```js
//	快速创建应用
npm i create-react-app -g
create-react-app react-app
cd react-app
npm run start

//	打包
npm run build

//	服务器启动
npm i http-server -g
http-server -p 3000 ./build
```



### 使用 Docker构建

> .dockerignore

```dockerfile
node_modules
build
```

> nginx.conf

```nginx
server {
    listen	80;
    server_name	locahost;
    
    location / {
        root 	/app/build;		#打包的路径
        index	index.html	index.htm
        try_files $uri $uri	/index.html;	#防止重刷新返回404
    }
    
    error_page	500	502	503	504 /50x.html;
    location = /50x.html{
        root	/usr/share/nginx/html
    }
}
```

> Dockerfile

```dockerfile
#	基于node11
FROM node:11

#	设置环境变量
ENV	PROJECT_ENV	production
ENV	NODE_ENV	production

#	安装nginx
RUN	apt-get	update && apt-get install -y nginx

#	把package.json package-loct.json	复制到app目录下
#	为了npm install 可以缓存
COPY pageage*.json /app/

#	切换app目录
WORKDIR	/app

#	安装依赖(通过淘宝镜像)
RUN npm install --registry=https://registry.npm.taobao.org

#	把所有源代码拷贝到/app
COPY .	/app

#	把所有的源代码拷贝到app
RUN npm run build 

#	拷贝配置文件到nginx
COPY	nginx.conf /etc/nginx/conf.d/default.conf

#	暴露的端口
EXPOSE	80

#	启动nginx,关闭守护式运行,否则容器启动后会立刻关闭
CMD ["nginx","-g","daemono off;"]
```



### 利用docker缓存

```dockerfile
#	right
COPY	package*.json /app/
RUN		npm instatll
COPY	. /app	
#	单独把package.json文件先拷贝到app安装完依赖,然后把所有的文件拷贝到app

#	bad
COPY	. /APP
RUN		npm install
#	每次重新构建镜像,都需要下载一次npm包.
#	而把package.json与源文件分隔开写入镜像,只有当package.json发生改变才会重新下载npm包


#	缓存有时候也会造成一些麻烦,比如在精心一些shell操作输出内容时,由于缓存的存在,到时新构建的镜像里的内容还是旧版
#	最佳实践是在文件顶部指个环境变量,如果希望不用缓存,则更新这个环境变量,因为缓存失效是从第一条变化指令开始
docker	build --no-cache -t deepred5/react-app.
```



### 打包镜像启动容器

```dockerfile
#	打包镜像
docker build -t deepred5/react-app .
#	启动容器
docker run -d name my-react-app	-p	8888:80	deepred5/react-app
#	访问页面
http://localhost:8888
http://localhost:8888/deepred5	#nginx配置
```



### 多层构建

<!--之前的Dockerfile,镜像基于node11,但是整个镜像用到node环境的地方只是为了前端打包,真正启动的是Nginx,镜像里的项目源代码,以及node_modules其实根本没有用,这些冗余文件造成了镜像的体积变得非常庞大-->

> Dockerfile.multi

```dockerfile
#	node仅仅用来打包文件
FROM	node:alpine as builder

ENV	PROJECT_ENV	production
ENV	NODE_ENV	production

WORKDIR	/app

RUN	npm install	--registry=https://registry.npm.taobao.org

COPY	. /app

RUN	npm run build

#	选择更小体积的基础镜像
FROM	nginx:alpine

COPY	nginx.conf /etc/nginx/conf.d/default.conf

COPY	--from=builder	/app/build	/app/build

#使用了两个FROM基础镜像,第一个node:alpine仅仅作为打包环境,真正的基础镜像是nginx:alpine

```

> 打包启动

```dockerfile
#	-f	s指定使用Dockerfile.multi进行构建
docker build -t deepred5/react-app-multi . -f Dockerfile.multi

#	启动容器
docker run -d --name my-react-app-multi	-p 8889:80	deepread5/react-app-multi

#	查看镜像大小
docker images deepred5/react-app-multi	#	20M+
docker images deepred5/react-app		#	1G+
```

