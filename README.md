# WebApp Contenerizada con Nodejs y MongoDB

## MongoDB

Ejecutamos la imagen mongo:lastest de MongoDB en un contenedor:

```js
$ docker run -d --name mongo mongo
```

## Crear la imagen Docker de nuestra aplicación

Creamos el fichero Dockerfile con el siguiente contenido:

```js
FROM node:10

# Create app directory
WORKDIR /usr/src/app

# Install app dependencies
COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 8080

CMD ["node", "index.js"]
```

Creamos la imagen con el comando:

```js
docker build -t jluisalvarez/node-webapp .
```

Lanzamos el contenedor con acceso a mongoDB

```js
 docker run -p 8080:8080 --name webapp --link mongo -d jluisalvarez/node-webapp
```


## Docker Compose

```js
version: "3"
services:
  web:
    # replace username/repo:tag with your name and image details
    image: username/repo:tag
    deploy:
      replicas: 5
      restart_policy:
        condition: on-failure
      resources:
        limits:
          cpus: "0.1"
          memory: 50M
    ports:
      - "80:8080"
    networks:
      - webnet
  visualizer:
    image: dockersamples/visualizer:stable
    ports:
      - "8080:8080"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    deploy:
      placement:
        constraints: [node.role == manager]
    networks:
      - webnet
  redis:
    image: mongo
  mongo:
    image: mongo
    volumes:
      - "/home/docker/data/db:/data/db"
    deploy:
      placement:
        constraints: [node.role == manager]
    command: --smallfiles
    networks:
      - webnet
networks:
  webnet:
```



## Desplegar servicios:

docker stack deploy -c docker-compose.yml webapp

## Mostrar listado de servicios

docker service ls

## Eliminar servicios

docker stack rm webapp

