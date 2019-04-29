# Desplegar App Contenerizada con Nodejs y MongoDB en un Clúste


## Docker Compose

```js
version: "3.7"
services:
  web:
    imagen: jluisalvarez/node-webapp
    build: .
    depends_on:
      - mongo
    deploy:
      replicas: 2
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
      - /var/run/docker.sock:/var/run/docker.sock
    deploy:
      placement:
        constraints: [node.role == manager]
    networks:
      - webnet

  mongo:
    image: mongo
    volumes:
      - db-data:/data/db
    deploy:
      placement:
        constraints: [node.role == manager]
    networks:
      - webnet

volumes:
  db-data:

networks:
  webnet:
```


## Desplegar servicios:

docker stack deploy -c docker-compose.yml webapp

## Mostrar listado de servicios

docker service ls

## Eliminar servicios

docker stack rm webapp

