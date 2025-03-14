🌞 Installez un WikiJS en utilisant Docker

WikiJS a besoin d'une base de données pour fonctionner
il faudra donc deux conteneurs : un pour WikiJS et un pour la base de données
référez-vous à la doc officielle de WikiJS, c'est tout guidé
```
sacha@test:~/partie3/wiki$ cat docker-compose.yaml
version: "3"
services:

  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: wiki
      POSTGRES_PASSWORD: wikijsrocks
      POSTGRES_USER: wikijs
    logging:
      driver: "none"
    restart: unless-stopped
    volumes:
      - db-data:/var/lib/postgresql/data

  wiki:
    image: ghcr.io/requarks/wiki:2
    depends_on:
      - db
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: wikijs
      DB_PASS: wikijsrocks
      DB_NAME: wiki
    restart: unless-stopped
    ports:
      - "8888:80"

volumes:
  db-data:
```
```
docker compose up -d 
```
🌞 Call me when it's done

je dois pouvoir visiter votre WikiJS (il doit être dispo sur internet)

🌞 Vous devez :

construire une image qui

contient python3

contient l'application et ses dépendances
lance l'application au démarrage du conteneur

écrire un docker-compose.yml qui définit le lancement de deux conteneurs :

l'app python
le Redis dont il a besoin
```
Dockerfile
FROM python:3.9

WORKDIR /app

COPY . /app

RUN pip install --no-cache-dir -r requirements.txt

EXPOSE 8888

CMD ["python","app.py"]
```
```
docker-compose.yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "8888:8888"
    depends_on:
      - db
    environment:
      - REDIS_HOST=db
      - REDIS_PORT=6379

  db:
    image: "redis:alpine"
    ports:
```
