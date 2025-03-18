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
```
sacha@test:~$ trivy image --security-checks vuln ghcr.io/requarks/wiki
2025-03-14T19:00:52Z    WARN    '--security-checks' is deprecated. Use '--scanners' instead.
2025-03-14T19:00:52Z    INFO    [vulndb] Need to update DB
2025-03-14T19:00:52Z    INFO    [vulndb] Downloading vulnerability DB...
2025-03-14T19:00:52Z    INFO    [vulndb] Downloading artifact...        repo="mirror.gcr.io/aquasec/trivy-db:2"
60.87 MiB / 60.87 MiB [-----------------------------------------------------------------------] 100.00% 4.38 MiB p/s 14s
2025-03-14T19:01:06Z    INFO    [vulndb] Artifact successfully downloaded       repo="mirror.gcr.io/aquasec/trivy-db:2"
2025-03-14T19:01:06Z    INFO    [vuln] Vulnerability scanning is enabled
2025-03-14T19:01:25Z    INFO    Detected OS     family="alpine" version="3.21.2"
2025-03-14T19:01:25Z    INFO    [alpine] Detecting vulnerabilities...   os_version="3.21" repository="3.21" pkg_num=71
2025-03-14T19:01:25Z    INFO    Number of language-specific files       num=1
2025-03-14T19:01:25Z    INFO    [node-pkg] Detecting vulnerabilities...
2025-03-14T19:01:26Z    WARN    Using severities from other vendors for some vulnerabilities. Read https://trivy.dev/v0.60/docs/scanner/vulnerability#severity-selection for details.
2025-03-14T19:01:26Z    INFO    Table result includes only package filenames. Use '--format json' option to get the full path to the package file.

Report Summary

┌──────────────────────────────────────────────────────────────────────────────────┬──────────┬─────────────────┐
│                                      Target                                      │   Type   │ Vulnerabilities │
├──────────────────────────────────────────────────────────────────────────────────┼──────────┼─────────────────┤
│ ghcr.io/requarks/wiki (alpine 3.21.2)                                            │  alpine  │       27        │
├──────────────────────────────────────────────────────────────────────────────────┼──────────┼─────────────────┤
```
```
sacha@test:~$ trivy image --security-checks vuln postgres
2025-03-14T19:03:26Z    WARN    '--security-checks' is deprecated. Use '--scanners' instead.
2025-03-14T19:03:26Z    INFO    [vuln] Vulnerability scanning is enabled
2025-03-14T19:03:32Z    INFO    Detected OS     family="debian" version="12.9"
2025-03-14T19:03:32Z    INFO    [debian] Detecting vulnerabilities...   os_version="12" pkg_num=147
2025-03-14T19:03:32Z    INFO    Number of language-specific files       num=1
2025-03-14T19:03:32Z    INFO    [gobinary] Detecting vulnerabilities...
2025-03-14T19:03:32Z    WARN    Using severities from other vendors for some vulnerabilities. Read https://trivy.dev/v0.60/docs/scanner/vulnerability#severity-selection for details.

Report Summary

┌────────────────────────┬──────────┬─────────────────┐
│         Target         │   Type   │ Vulnerabilities │
├────────────────────────┼──────────┼─────────────────┤
│ postgres (debian 12.9) │  debian  │       152       │
├────────────────────────┼──────────┼─────────────────┤
│ usr/local/bin/gosu     │ gobinary │       58        │
└────────────────────────┴──────────┴─────────────────┘
Legend:
```
```
sacha@test:~$ trivy image --security-checks vuln apache-alpine
2025-03-14T19:04:16Z    WARN    '--security-checks' is deprecated. Use '--scanners' instead.
2025-03-14T19:04:16Z    INFO    [vuln] Vulnerability scanning is enabled
2025-03-14T19:04:17Z    INFO    Detected OS     family="alpine" version="3.21.3"
2025-03-14T19:04:17Z    INFO    [alpine] Detecting vulnerabilities...   os_version="3.21" repository="3.21" pkg_num=21
2025-03-14T19:04:17Z    INFO    Number of language-specific files       num=0

Report Summary

┌───────────────────────────────┬────────┬─────────────────┐
│            Target             │  Type  │ Vulnerabilities │
├───────────────────────────────┼────────┼─────────────────┤
│ apache-alpine (alpine 3.21.3) │ alpine │        0        │
└───────────────────────────────┴────────┴─────────────────┘
Legend:
- '-': Not scanned
- '0': Clean (no security findings detected)

```
```
sacha@test:~$ trivy image --security-checks vuln nginx:latest
2025-03-14T19:05:09Z    WARN    '--security-checks' is deprecated. Use '--scanners' instead.
2025-03-14T19:05:09Z    INFO    [vuln] Vulnerability scanning is enabled
2025-03-14T19:05:39Z    INFO    [javadb] Downloading Java DB...
2025-03-14T19:05:39Z    INFO    [javadb] Downloading artifact...        repo="mirror.gcr.io/aquasec/trivy-java-db:1"
697.86 MiB / 697.86 MiB [-----------------------------------------------------------------------------------------------------] 100.00% 8.68 MiB p/s 1m21s
2025-03-14T19:07:01Z    INFO    [javadb] Artifact successfully downloaded       repo="mirror.gcr.io/aquasec/trivy-java-db:1"
2025-03-14T19:07:01Z    INFO    [javadb] Java DB is cached for 3 days. If you want to update the database more frequently, "trivy clean --java-db" command clears the DB cache.
2025-03-14T19:07:01Z    INFO    Detected OS     family="debian" version="12.9"
2025-03-14T19:07:01Z    INFO    [debian] Detecting vulnerabilities...   os_version="12" pkg_num=149
2025-03-14T19:07:01Z    INFO    Number of language-specific files       num=0
2025-03-14T19:07:01Z    WARN    Using severities from other vendors for some vulnerabilities. Read https://trivy.dev/v0.60/docs/scanner/vulnerability#severity-selection for details.

Report Summary

┌────────────────────────────┬────────┬─────────────────┐
│           Target           │  Type  │ Vulnerabilities │
├────────────────────────────┼────────┼─────────────────┤
│ nginx:latest (debian 12.9) │ debian │       157       │
└────────────────────────────┴────────┴─────────────────┘
Legend:
```
3. Petit benchmark secu
Il existe plusieurs référentiels pour sécuriser une machine donnée qui utilise un OS donné. Un savoir particulièrement recherché pour renforcer la sécurité des serveurs surtout.
Un des référentiels réputé et disponible en libre accès, ce sont les benchmarks de CIS. Ce sont ni plus ni moins que des guides complets pour sécuriser de façon assez forte une machine qui tourne par exemple sous Debian, Rocky Linux ou bien d'autres.
Docker développe un petit outil qui permet de vérifier si votre utilisation de Docker est compatible avec les recommandations de CIS.
🌞 Utilisez l'outil Docker Bench for Security

rien à me mettre en rendu, je vous laisse exprimer votre curiosité quant aux résultats
ce genre d'outils est cool d'un point de vue pédagogique : chaque check que fait le script c'est un truc à savoir finalement !
Holy moly, ça fait pas mal de Warn en tout cas [I'm in Danger] 
