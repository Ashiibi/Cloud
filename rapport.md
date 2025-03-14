🌞 Construire votre propre image

image de base (celle que vous voulez : debian, alpine, ubuntu, etc.)
une image du Docker Hub
qui ne porte aucune application par défaut
vous ajouterez

mise à jour du système
installation de Apache (pour les systèmes debian, le serveur Web apache s'appelle apache2 et non pas httpd comme sur Rocky)
page d'accueil Apache HTML personnalisée

```
FROM alpine:latest

RUN apk update -y && apk add apache2 -y

COPY index.html /var/www/localhost/htdocs

EXPOSE 80

CMD ["httpd", "-D", "FOREGROUND"]
```
```
docker build -t apache-alpine .
docker run -d -p 8888:80 apache-alpine
```