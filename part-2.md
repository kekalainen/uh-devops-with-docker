# 2.1

`docker-compose.yml`

```yaml
version: "3.9"
services:
    web:
        image: devopsdockeruh/simple-web-service
        volumes:
            - ./text.log:/usr/src/app/text.log
```

```sh
kekalainen@Z97:~$ touch text.log
kekalainen@Z97:~$ docker-compose up
Creating network "kekalainen_default" with the default driver
Creating kekalainen_web_1 ... done
Attaching to kekalainen_web_1
web_1  | Starting log output
web_1  | Wrote text to /usr/src/app/text.log
^CGracefully stopping... (press Ctrl+C again to force)
Stopping kekalainen_web_1 ... done
```


# 2.2

`docker-compose.yml`

```yaml
version: "3.9"
services:
    web:
        image: devopsdockeruh/simple-web-service
        ports:
            - 8080:8080
        command: server
```

```sh
kekalainen@Z97:~$ docker-compose up
Recreating kekalainen_web_1 ... done
Attaching to kekalainen_web_1
web_1  | [GIN-debug] [WARNING] Creating an Engine instance with the Logger and Recovery middleware already attached.
web_1  |
web_1  | [GIN-debug] [WARNING] Running in "debug" mode. Switch to "release" mode in production.
web_1  |  - using env:  export GIN_MODE=release
web_1  |  - using code: gin.SetMode(gin.ReleaseMode)
web_1  |
web_1  | [GIN-debug] GET    /*path                    --> server.Start.func1 (3 handlers)
web_1  | [GIN-debug] Listening and serving HTTP on :8080
```

# 2.3

`example-frontend/Dockerfile`

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

`example-backend/Dockerfile`

```dockerfile
FROM golang:1.16-alpine
WORKDIR /go/src/app
COPY . .
RUN go build
EXPOSE 8080
CMD ./server
```

`docker-compose.yml`

```yaml
version: "3.9"
services:
    frontend:
        build: ./example-frontend
        ports:
            - 5000:5000
    backend:
        build: ./example-backend
        ports:
            - 8080:8080
        environment:
            - REQUEST_ORIGIN=http://localhost:5000
```

```sh
kekalainen@Z97:~/material-applications$ docker-compose up -d
Creating network "material-applications_default" with the default driver
Creating material-applications_frontend_1 ... done
Creating material-applications_backend_1  ... done
```

# 2.4

`docker-compose.yml`

```yaml
version: "3.9"
services:
    frontend:
        build: ./example-frontend
        ports:
            - 5000:5000
    backend:
        build: ./example-backend
        ports:
            - 8080:8080
        environment:
            - REQUEST_ORIGIN=http://localhost:5000
            - REDIS_HOST=cache
    cache:
        image: redis:6.2.4-alpine
```

# 2.5

```sh
kekalainen@Z97:~/material-applications/scaling-exercise$ docker-compose up -d --scale compute=4
Creating network "scaling-exercise_default" with the default driver
Creating calculator                 ... done
Creating load-balancer              ... done
Creating scaling-exercise_compute_1 ... done
Creating scaling-exercise_compute_2 ... done
Creating scaling-exercise_compute_3 ... done
Creating scaling-exercise_compute_4 ... done
```

# 2.6

`docker-compose.yml`

```yaml
version: "3.9"
services:
    frontend:
        build: ./example-frontend
        ports:
            - 5000:5000
    backend:
        build: ./example-backend
        ports:
            - 8080:8080
        environment:
            - REQUEST_ORIGIN=http://localhost:5000
            - REDIS_HOST=cache
            - POSTGRES_HOST=database
    cache:
        image: redis:6.2.4-alpine
    database:
        image: postgres:13
        environment:
            - POSTGRES_USER=postgres
            - POSTGRES_PASSWORD=postgres
```

# 2.7

`docker-compose.yml`

```yaml
version: "3.9"
services:
    frontend:
        build: ./ml-kurkkumopo-frontend
        ports:
            - 3000:3000
    backend:
        build: ./ml-kurkkumopo-backend
        ports:
            - 5000:5000
        volumes:
            - ./model:/src/model
    training:
        build: ./ml-kurkkumopo-training
        volumes:
            - ./model:/src/model
            - ./imgs:/src/imgs
```

# 2.8

`nginx.conf`
```nginx
events {
    worker_connections 1024;
}

http {
    server {
        listen 80;

        location / {
            proxy_pass http://frontend:5000/;
        }

        location /api/ {
            proxy_set_header Host $host;
            proxy_pass http://backend:8080/;
        }
    }
}
```

`docker-compose.yml`

```yaml
version: "3.9"
services:
    frontend:
        build: ./example-frontend
    backend:
        build: ./example-backend
        ports:
            - 8080:8080
        environment:
            - REQUEST_ORIGIN=http://localhost
            - REDIS_HOST=cache
            - POSTGRES_HOST=database
    cache:
        image: redis:6.2.4-alpine
    database:
        image: postgres:13
        environment:
            - POSTGRES_USER=postgres
            - POSTGRES_PASSWORD=postgres
    reverse-proxy:
        image: nginx:1.21
        ports:
            - 80:80
        volumes:
            - ./nginx.conf:/etc/nginx/nginx.conf:ro
```

# 2.9


`docker-compose.yml`

```yaml
version: "3.9"
services:
    frontend:
        build: ./example-frontend
    backend:
        build: ./example-backend
        ports:
            - 8080:8080
        environment:
            - REQUEST_ORIGIN=http://localhost
            - REDIS_HOST=cache
            - POSTGRES_HOST=database
    cache:
        image: redis:6.2.4-alpine
        volumes:
            - ./storage/cache:/data
        command: redis-server --appendonly yes
    database:
        image: postgres:13
        environment:
            - POSTGRES_USER=postgres
            - POSTGRES_PASSWORD=postgres
        volumes:
            - ./storage/database:/var/lib/postgresql/data
    reverse-proxy:
        image: nginx:1.21
        ports:
            - 80:80
        volumes:
            - ./nginx.conf:/etc/nginx/nginx.conf:ro
```

# 2.10

The URL in the `REQUEST_ORIGIN` environment variable was originally configured to permit requests from the frontend's origin `http://localhost:5000`. As a quick fix it was changed to `http://localhost` above to reflect the new request origin in the reverse proxied setup.

A proper solution would be updating the frontend `Dockerfile` as follows:

```dockerfile
FROM node:14-slim
RUN npm i -g serve
COPY . .
RUN npm ci
RUN npm run build
EXPOSE 5000
CMD serve -s -l 5000 build
```

(removing the line `ENV REACT_APP_BACKEND_URL=http://localhost:8080`)

and rebuilding the container.

```sh
kekalainen@Z97:~/material-applications$ docker-compose build
cache uses an image, skipping
database uses an image, skipping
reverse-proxy uses an image, skipping
Building frontend
[+] Building 89.4s (10/10) FINISHED
```

This way all API requests are sent to `/api` on the same origin (e.g. `http://localhost/api`) and the `REQUEST_ORIGIN` environment variable can be omitted from the backend configuration in `docker-compose.yml`. Ports don't need to be published for the backend and frontend containers directly, either:

```yaml
version: "3.9"
services:
    frontend:
        build: ./example-frontend
    backend:
        build: ./example-backend
        environment:
            - REDIS_HOST=cache
            - POSTGRES_HOST=database
    cache:
        image: redis:6.2.4-alpine
        volumes:
            - ./storage/cache:/data
        command: redis-server --appendonly yes
    database:
        image: postgres:13
        environment:
            - POSTGRES_USER=postgres
            - POSTGRES_PASSWORD=postgres
        volumes:
            - ./storage/database:/var/lib/postgresql/data
    reverse-proxy:
        image: nginx:1.21
        ports:
            - 80:80
        volumes:
            - ./nginx.conf:/etc/nginx/nginx.conf:ro
```

# 2.11

(skipped)
