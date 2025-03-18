1. Install
🌞 Installer Docker votre machine Azure
```
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo systemctl start docker

```
démarrer le service docker avec une commande systemctl
```
sudo systemctl start docker
```
ajouter votre utilisateur au groupe docker
```
sudo usermod -aG docker $(whoami)
```
cela permet d'utiliser Docker sans avoir besoin de l'identité de root
déconnectez-vous puis relancez une session pour que le changement prenne effet

🌞 Utiliser la commande docker run

lancer un conteneur nginx
par défaut il écoute sur le port 80 et propose une page d'accueil
le conteneur doit être lancé avec un partage de port
le port 9999 de la machine hôte doit rediriger vers le port 80 du conteneur
```
sacha@test:~$ docker run -d -p 9999:80 nginx
8db0a61bd10a4ccc01f2e3df17d9e9026ad70d8625d388892e9232b2ef197eaa
```

🌞 Rendre le service dispo sur internet
il faut peut-être ouvrir un port firewall dans votre VM (suivant votre OS, ptet y'en a un, ptet pas)
-> firewall inactif donc no need
il faut ouvrir un port dans l'interface web de Azure (appelez moi si vous trouvez pas)
vous devez pouvoir le visiter avec votre navigateur (un curl m'ira bien pour le compte-rendu)
```
sacha>curl 135.236.96.80
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
🌞 Custom un peu le lancement du conteneur

l'app NGINX doit avoir un fichier de conf personnalisé pour écouter sur le port 7777 (pas le port 80 par défaut)
l'app NGINX doit servir un fichier index.html personnalisé (pas le site par défaut)
l'application doit être joignable grâce à un partage de ports (vers le port 7777)
vous limiterez l'utilisation de la RAM du conteneur à 512M
le conteneur devra avoir un nom : meow
```
sacha@test:~$ docker run -d --name meow -p 7777:7777 --memory=512M -v $(pwd)/index.html:/var/www/tp_docker/index.html -v $(pwd)/toto.conf:/etc/nginx/conf.d/nginx.conf nginx:latest
4bfa08631ffc54e90db26b52ddcafc7a2399d46ece11da7fef91ec0e3650b4e0
```
