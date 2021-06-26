# 1.1

```sh
kekalainen@Z97:~$ docker ps -a
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS                      PORTS      NAMES
0097f21fef9e   redis     "docker-entrypoint.s…"   34 seconds ago       Up 33 seconds               6379/tcp   hardcore_swanson
5068eef07384   redis     "docker-entrypoint.s…"   47 seconds ago       Exited (1) 46 seconds ago              sad_nash
f93667057851   redis     "docker-entrypoint.s…"   About a minute ago   Exited (1) 55 seconds ago              vibrant_thompson
```

# 1.2

```sh
kekalainen@Z97:~$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
kekalainen@Z97:~$ docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
```

# 1.3

```sh
kekalainen@Z97:~$ docker run -d devopsdockeruh/simple-web-service:ubuntu
ace93ae06d520a4c2170b06660d548e21a5e769c72cfc494cfc2006a590f3d89
kekalainen@Z97:~$ docker ps -a
CONTAINER ID   IMAGE                                      COMMAND                 CREATED         STATUS          PORTS     NAMES
ace93ae06d52   devopsdockeruh/simple-web-service:ubuntu   "/usr/src/app/server"   2 minutes ago   Up 23 seconds             dreamy_johnson
kekalainen@Z97:~$ docker exec -it dreamy_johnson bash
root@ace93ae06d52:/usr/src/app# tail -f ./text.log
2021-06-26 13:40:15 +0000 UTC
2021-06-26 13:40:17 +0000 UTC
Secret message is: 'You can find the source code here: https://github.com/docker-hy'
```

# 1.4

```sh
kekalainen@Z97:~$ docker run -itd ubuntu sh -c 'echo "Input website:"; read website; echo "Searching.."; sleep 1; curl http://$website;'
a1cab648f58db4210d63cd0b3a3ef068be33d7de133acb275a805e0394a25ea7
kekalainen@Z97:~$ docker exec -it a1cab648f58db4210d63cd0b3a3ef068be33d7de133acb275a805e0394a25ea7 bash
root@a1cab648f58d:/# apt-get update > /dev/null && apt-get install -y curl > /dev/null
debconf: delaying package configuration, since apt-utils is not installed
root@a1cab648f58d:/# exit
exit
kekalainen@Z97:~$ docker attach a1cab648f58db4210d63cd0b3a3ef068be33d7de133acb275a805e0394a25ea7
helsinki.fi
Searching..
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>301 Moved Permanently</title>
</head><body>
<h1>Moved Permanently</h1>
<p>The document has moved <a href="https://www.helsinki.fi/">here</a>.</p>
</body></html>
```

# 1.5

```sh
kekalainen@Z97:~$ docker image ls | grep web
devopsdockeruh/simple-web-service   ubuntu    4e3362e907d5   3 months ago   83MB
devopsdockeruh/simple-web-service   alpine    fd312adc88e0   3 months ago   15.7MB
```

```sh
kekalainen@Z97:~$ docker run -d devopsdockeruh/simple-web-service:alpine
43b58a2212f67c01a7c37772ae94c2f4b2b7b4107d427c0ccfeea4e11d7e6b03
kekalainen@Z97:~$ docker exec -it 43b58a2212f67c01a7c37772ae94c2f4b2b7b4107d427c0ccfeea4e11d7e6b03 sh
/usr/src/app # ls
server    text.log
/usr/src/app # cat ./text.log
2021-06-26 14:11:47 +0000 UTC
2021-06-26 14:11:49 +0000 UTC
2021-06-26 14:11:51 +0000 UTC
2021-06-26 14:11:53 +0000 UTC
2021-06-26 14:11:55 +0000 UTC
Secret message is: 'You can find the source code here: https://github.com/docker-hy'
```

# 1.6

```sh
kekalainen@Z97:~$ docker run -it devopsdockeruh/pull_exercise
Give me the password: basics
You found the correct password. Secret message is:
"This is the secret message"

```

# 1.7

```dockerfile
FROM devopsdockeruh/simple-web-service:alpine
CMD server
```

```sh
kekalainen@Z97:~$ docker build . -t web-server
[+] Building 0.1s (5/5) FINISHED
 => [internal] load build definition from Dockerfile                                                                                                                                                            0.0s 
 => => transferring dockerfile: 37B                                                                                                                                                                             0.0s 
 => [internal] load .dockerignore                                                                                                                                                                               0.0s 
 => => transferring context: 2B                                                                                                                                                                                 0.0s 
 => [internal] load metadata for docker.io/devopsdockeruh/simple-web-service:alpine                                                                                                                             0.0s 
 => CACHED [1/1] FROM docker.io/devopsdockeruh/simple-web-service:alpine                                                                                                                                        0.0s 
 => exporting to image                                                                                                                                                                                          0.0s 
 => => exporting layers                                                                                                                                                                                         0.0s 
 => => writing image sha256:978fbf315695ef5a3ec2e77ee411c4dcd9aa9b867fbc7ea3d26962545fda0585                                                                                                                    0.0s 
 => => naming to docker.io/library/web-server                                                                                                                                                                   0.0s 

Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
kekalainen@Z97:~$ docker run web-server
[GIN-debug] [WARNING] Creating an Engine instance with the Logger and Recovery middleware already attached.

[GIN-debug] [WARNING] Running in "debug" mode. Switch to "release" mode in production.
 - using env:   export GIN_MODE=release
 - using code:  gin.SetMode(gin.ReleaseMode)

[GIN-debug] GET    /*path                    --> server.Start.func1 (3 handlers)
[GIN-debug] Listening and serving HTTP on :8080
```

# 1.8

`script.sh`

```sh
echo "Input website:"; read website; echo "Searching.."; sleep 1; curl http://$website;
```

`Dockerfile`

```dockerfile
FROM ubuntu:18.04
RUN apt-get update && apt-get install -y curl
COPY script.sh .
CMD ./script.sh
```

```sh
kekalainen@Z97:~$ docker run -it curler
Input website:
helsinki.fi
Searching..
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>301 Moved Permanently</title>
</head><body>
<h1>Moved Permanently</h1>
<p>The document has moved <a href="https://www.helsinki.fi/">here</a>.</p>
</body></html>
```

# 1.9

```sh
kekalainen@Z97:~$ touch text.log
kekalainen@Z97:~$ docker run -v "$(pwd)/text.log:/usr/src/app/text.log" devopsdockeruh/simple-web-service
Starting log output
Wrote text to /usr/src/app/text.log
```

# 1.10


```sh
kekalainen@Z97:~$ docker run -d -p 8080:8080 web-server
3a4ba70f64c6f7736084311c4527638c7a3d59c1c60da308584c4b89ce7ae55f
kekalainen@Z97:~$ curl 127.0.0.1:8080
{"message":"You connected to the following path: /","path":"/"}
```

# 1.11

```sh
kekalainen@Z97:~$ git clone https://github.com/docker-hy/material-applications.git
Cloning into 'material-applications'...
remote: Enumerating objects: 526, done.
remote: Counting objects: 100% (526/526), done.
remote: Compressing objects: 100% (333/333), done.
remote: Total 526 (delta 169), reused 477 (delta 122), pack-reused 0
Receiving objects: 100% (526/526), 486.75 KiB | 2.59 MiB/s, done.
Resolving deltas: 100% (169/169), done.
Updating files: 100% (215/215), done.
kekalainen@Z97:~$ cd ./material-applications/spring-example-project/
```

`Dockerfile`

```dockerfile
FROM openjdk:8-slim
COPY . .
RUN ./mvnw package
EXPOSE 8080
CMD java -jar ./target/docker-example-1.1.3.jar
```

```sh
kekalainen@Z97:~/material-applications/spring-example-project$ docker build . -t spring-example-project
```

```sh
kekalainen@Z97:~/material-applications/spring-example-project$ docker run -d -p 8080:8080 spring-example-project
d97d09cfe28f25e84f013e841b049128c2cea5ed70c0fd65be602505b05b5fd2
kekalainen@Z97:~/material-applications/spring-example-project$ curl -X POST 127.0.0.1:8080/press
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">        <title>Spring</title>
        <meta charset="UTF-8" />

    </head>
    <body style="width: fit-content; padding-top: 3em; margin: auto;">
        <form action="/press" method="post">
            <button class="btn btn-info btn-outline-dark" type="submit">Press here</button>
        </form>
        <p style="width: fit-content; margin: auto;">Success</p>
    </body>
</html>
```

# 1.12

`Dockerfile`

```dockerfile
FROM node:14-slim
RUN npm i -g serve
COPY . .
RUN npm ci
RUN npm run build
EXPOSE 5000
CMD serve -s -l 5000 build
```

```sh
kekalainen@Z97:~/material-applications/example-frontend$ docker build . -t example-frontend
[+] Building 11.8s (10/10) FINISHED
 => [internal] load build definition from Dockerfile                                                                                                                                                            0.3s 
 => => transferring dockerfile: 37B                                                                                                                                                                             0.0s 
 => [internal] load .dockerignore                                                                                                                                                                               0.3s 
 => => transferring context: 34B                                                                                                                                                                                0.0s 
 => [internal] load metadata for docker.io/library/node:14-slim                                                                                                                                                11.2s 
 => [1/5] FROM docker.io/library/node:14-slim@sha256:41bbb713cb3ac5f7d531ccdc156d2611f019b50b9c478d6475c79ced618640f0                                                                                           0.0s 
 => [internal] load build context                                                                                                                                                                               0.2s 
 => => transferring context: 1.21kB                                                                                                                                                                             0.1s 
 => CACHED [2/5] RUN npm i -g serve                                                                                                                                                                             0.0s 
 => CACHED [3/5] COPY . .                                                                                                                                                                                       0.0s 
 => CACHED [4/5] RUN npm ci                                                                                                                                                                                     0.0s 
 => CACHED [5/5] RUN npm run build                                                                                                                                                                              0.0s 
 => exporting to image                                                                                                                                                                                          0.0s 
 => => exporting layers                                                                                                                                                                                         0.0s 
 => => writing image sha256:3e403f1fa8cecc66da4c10213941fd76d43488543f5261f012aeb07d55b3d240                                                                                                                    0.0s 
 => => naming to docker.io/library/example-frontend                                                                                                                                                             0.0s 

Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
kekalainen@Z97:~/material-applications/example-frontend$ docker run -d -p 5000:5000 example-frontend
22eb2f6f52d3db810cff6b34530202e83557ae94ab702eb52e40908ecb7a2c1f
kekalainen@Z97:~/material-applications/example-frontend$ curl localhost:5000
<!doctype html><html lang="en"><head><meta charset="utf-8"/><meta name="viewport" content="width=device-width,initial-scale=1"/><meta name="theme-color" content="#000000"/><meta name="description" content="Frontend for docker course"/><link rel="manifest" href="/manifest.json"/><title>Docker frontend</title><link href="/static/css/main.eaa5d75e.chunk.css" rel="stylesheet"></head><body><noscript>You need to enable JavaScript to run this app.</noscript><div id="root"></div><script>!function(e){function r(r){for(var n,l,f=r[0],a=r[1],p=r[2],c=0,s=[];c<f.length;c++)l=f[c],Object.prototype.hasOwnProperty.call(o,l)&&o[l]&&s.push(o[l][0]),o[l]=0;for(n in a)Object.prototype.hasOwnProperty.call(a,n)&&(e[n]=a[n]);for(i&&i(r);s.length;)s.shift()();return u.push.apply(u,p||[]),t()}function t(){for(var e,r=0;r<u.length;r++){for(var t=u[r],n=!0,f=1;f<t.length;f++){var a=t[f];0!==o[a]&&(n=!1)}n&&(u.splice(r--,1),e=l(l.s=t[0]))}return e}var n={},o={1:0},u=[];function l(r){if(n[r])return n[r].exports;var t=n[r]={i:r,l:!1,exports:{}};return e[r].call(t.exports,t,t.exports,l),t.l=!0,t.exports}l.m=e,l.c=n,l.d=function(e,r,t){l.o(e,r)||Object.defineProperty(e,r,{enumerable:!0,get:t})},l.r=function(e){"undefined"!=typeof Symbol&&Symbol.toStringTag&&Object.defineProperty(e,Symbol.toStringTag,{value:"Module"}),Object.defineProperty(e,"__esModule",{value:!0})},l.t=function(e,r){if(1&r&&(e=l(e)),8&r)return e;if(4&r&&"object"==typeof e&&e&&e.__esModule)return e;var t=Object.create(null);if(l.r(t),Object.defineProperty(t,"default",{enumerable:!0,value:e}),2&r&&"string"!=typeof e)for(var n in e)l.d(t,n,function(r){return e[r]}.bind(null,n));return t},l.n=function(e){var r=e&&e.__esModule?function(){return e.default}:function(){return e};return l.d(r,"a",r),r},l.o=function(e,r){return Object.prototype.hasOwnProperty.call(e,r)},l.p="/";var f=this["webpackJsonpexample-frontend"]=this["webpackJsonpexample-frontend"]||[],a=f.push.bind(f);f.push=r,f=f.slice();for(var p=0;p<f.length;p++)r(f[p]);var i=a;t()}([])</script><script src="/static/js/2.43ca3586.chunk.js"></script><script src="/static/js/main.f5c219c4.chunk.js"></script></body></html>
```

# 1.13

`Dockerfile`

```dockerfile
FROM golang:1.16-alpine
WORKDIR /go/src/app
COPY . .
RUN go build
EXPOSE 8080
CMD ./server
```

```sh
kekalainen@Z97:~/material-applications/example-backend$ docker build . -t example-backend
[+] Building 11.4s (9/9) FINISHED
 => [internal] load build definition from Dockerfile                                                                                                                                                            0.1s 
 => => transferring dockerfile: 37B                                                                                                                                                                             0.0s 
 => [internal] load .dockerignore                                                                                                                                                                               0.0s 
 => => transferring context: 34B                                                                                                                                                                                0.0s 
 => [internal] load metadata for docker.io/library/golang:1.16-alpine                                                                                                                                          11.2s 
 => [internal] load build context                                                                                                                                                                               0.1s 
 => => transferring context: 499B                                                                                                                                                                               0.0s
 => [1/4] FROM docker.io/library/golang:1.16-alpine@sha256:45f32e963bb3cc408cfcd01a8e76b2872fb238f602ec5481cd75393da29369c0                                                                                     0.0s 
 => CACHED [2/4] WORKDIR /go/src/app                                                                                                                                                                            0.0s
 => CACHED [3/4] COPY . .                                                                                                                                                                                       0.0s 
 => CACHED [4/4] RUN go build                                                                                                                                                                                   0.0s 
 => exporting to image                                                                                                                                                                                          0.0s 
 => => exporting layers                                                                                                                                                                                         0.0s 
 => => writing image sha256:31abdcec712f6dc22e4f44ca7924092a6af52f676f590c30ce42d76886632afb                                                                                                                    0.0s 
 => => naming to docker.io/library/example-backend                                                                                                                                                              0.0s 

Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
kekalainen@Z97:~/material-applications/example-backend$ docker run -d -p 8080:8080 example-backend
eb293408875aea25e8f271d464f6f6fbabfdeee53349e116ab0a8cea985c6a93
kekalainen@Z97:~/material-applications/example-backend$ curl localhost:8080/ping
pong
```

# 1.14

## Frontend

`Dockerfile`

```dockerfile
FROM node:14-slim
RUN npm i -g serve
COPY . .
RUN npm ci
ENV REACT_APP_BACKEND_URL=http://localhost:8080
RUN npm run build
EXPOSE 5000
CMD serve -s -l 5000 build
```

```sh
kekalainen@Z97:~/material-applications/example-frontend$ docker build . -t example-frontend
```

```sh
kekalainen@Z97:~/material-applications/example-frontend$ docker run -d -p 5000:5000 example-frontend
85cc3e228023453473e5a0ad9d845e0106460592d5bb5bfd56fa1f770b29c11f
```

## Backend

```sh
kekalainen@Z97:~/material-applications/example-backend$ docker run -d -p 8080:8080 -e REQUEST_ORIGIN=http://localhost:5000 example-backend
8719173036bedde6b3e8cf5c22c09cf855d3aff28ea9ec613a97a26672519ce8
```

# 1.15

(skipped)

# 1.16

```sh
kekalainen@Z97:~$ docker tag devopsdockeruh/heroku-example registry.heroku.com/uh-devops-with-docker/web
```

[uh-devops-with-docker](https://uh-devops-with-docker.herokuapp.com/)
