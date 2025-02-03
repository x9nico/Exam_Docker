# 🚀 Exercices Docker

## 🟢 Facile (400 points)

### 1. Lancer un conteneur simple (80 pts)
**Objectif** : Démarrer un conteneur à partir d’une image minimale et exécuter une commande.

**Explication** : Docker permet d’exécuter des applications dans des conteneurs isolés. Ici, nous utilisons une image ultra-légère, `alpine`, pour afficher un message.

**Commande** :
```sh
docker run alpine echo "Hello, Docker!"
```

**Réponse** :

```sh
Hello, Docker!
```

### 2. Exposer un service web (80 pts)
**Objectif** : Lancer un conteneur Nginx et le rendre accessible depuis l’hôte sur un port spécifique.

**Explication** : Les conteneurs fonctionnent en isolation, mais vous pouvez les rendre accessibles via des ports mappés. L’image nginx contient un serveur HTTP.

Commande :

```sh
docker run -d -p 8080:80 nginx
```

**Réponse** : http://localhost:8080

### 3. Construire une image Docker personnalisée (80 pts)
**Objectif** : Créer une image contenant une page HTML statique et la tester.

**Explication** : Un Dockerfile permet de personnaliser une image. Vous allez copier une page HTML dans une image nginx et l’exécuter.

Voir fichier `1-3/Dockerfile` et `index.html`.

Commande (à la racine du projet) :

```sh
docker build -t custom-nginx .
docker run -d -p 8080:80 custom-nginx
```

**Réponse** : http://localhost:8080


### 4. Utiliser un volume persistant (80 pts)

**Objectif** : Stocker des fichiers persistants dans un volume pour ne pas perdre les données après l'arrêt du conteneur.

**Explication** : Par défaut, les données dans un conteneur sont perdues à sa suppression. Un volume Docker permet de stocker les fichiers même après l’arrêt.

Commande :

```sh
docker volume create mydata
docker run -d -v mydata:/data alpine sh -c "echo 'Données persistantes' > /data/file.txt"
docker run --rm -v mydata:/data alpine cat /data/file.txt
```

**Réponse** : 

```sh
Données persistantes
```



### 5. Utiliser une variable d’environnement (80 pts)
**Objectif** : Passer une variable d’environnement dans un conteneur et l’afficher.

**Explication** : Les applications conteneurisées peuvent recevoir des paramètres via des variables d’environnement.

Commande :

```sh
docker run --rm -e MESSAGE="Bonjour, Docker!" alpine sh -c 'echo $MESSAGE'
```

**Réponse** :

```sh
Bonjour, Docker!
```

## 🟠 Moyen (600 points)

### 6. Connecter deux conteneurs avec un réseau bridge (120 pts)
**Objectif** : Permettre à deux conteneurs de communiquer en utilisant un réseau Docker.

**Explication** : Par défaut, chaque conteneur est isolé. En créant un réseau Docker, on permet la communication entre eux.

Commande :

```sh
docker network create mynetwork
docker run -d --name web --network mynetwork nginx
docker run --rm --network mynetwork alpine sh -c "ping -c 3 web"
```

**Réponse** :
```sh
PING web (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.315 ms
64 bytes from 172.18.0.2: seq=1 ttl=64 time=0.088 ms
64 bytes from 172.18.0.2: seq=2 ttl=64 time=0.089 ms

--- web ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.088/0.164/0.315 ms
```


### 7. Construire et utiliser un Dockerfile multi-stage (120 pts)

**Objectif** : Réduire la taille d’une image en utilisant la construction multi-stage.

**Explication** : Un Dockerfile multi-stage compile une application dans un conteneur temporaire, puis copie uniquement les fichiers nécessaires.

Présents dans le dossier `2-7`.

Commande :

```sh
docker build -t myapp .
docker run --rm myapp
```

**Réponse** :

```sh
Hello, Docker Multi-Stage!
```


### 8. Configurer un Docker Compose (120 pts)

**Objectif** : Déployer une application avec plusieurs services en utilisant docker-compose.

**Explication** : `docker-compose` facilite le déploiement d’applications multi-conteneurs, comme une base de données et une API.

Présent dans le dossier `2-8`

Commande :

```sh
docker-compose up -d
```

**Réponse** :

```sh
WARN[0000] /mnt/c/laragon/www/Exam_Docker/2-8/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
[+] Running 11/1
 ✔ db Pulled                                                                                                                                                    83.9s 
[+] Running 3/3
 ✔ Network 2-8_default  Created                                                                                                                                  0.3s 
 ✔ Container 2-8-web-1  Started                                                                                                                                  1.5s 
 ✔ Container 2-8-db-1   Started                                                                                                                                  1.5s 
```


### 9. Sécuriser une image avec un utilisateur non-root (120 pts)

**Objectif** : Exécuter un conteneur en utilisant un utilisateur non-root pour améliorer la sécurité.

**Explication** : Lancer des conteneurs avec un utilisateur non-root réduit les risques de sécurité en cas de compromission.

Présent dans le `2-9`

Commande :

```sh 
docker build -t secure-nginx .
docker run -d -p 8080:80 secure-nginx
```

**Réponse** : http://localhost:8080

## 🔴 Difficile (500 points)

### 10. Créer un registre Docker privé (100 pts)

**Objectif** : Héberger vos propres images Docker sur un registre privé.

**Explication** : Un registre Docker privé permet de stocker et distribuer vos propres images de manière sécurisée.

Commande :

```sh
docker run -d -p 5000:5000 --name registry registry:2
curl http://localhost:5000/v2/
```

**Réponse**: 

```sh
{}
```

### 11. Construire une image avec Buildah (100 pts)

**Objectif** : Construire une image sans utiliser Docker en utilisant Buildah.

**Explication**: Buildah est un outil open-source permettant de construire des images sans nécessiter un démon Docker.

Commande :

```sh 
buildah from alpine:latest
buildah containers # Colonne CONTAINER_NAME
buildah run alpine-working-container apk add --no-cache curl
buildah config --author="Nicolas RAYNAUD <email@example.com>" --cmd "/bin/sh" alpine-working-container
buildah commit alpine-working-container  my-custom-image:1.0
```

**Réponse** :
```sh
nico@NICO-ASUS:/mnt/c/laragon/www/Exam_Docker$ buildah containers # Colonne CONTAINER_NAME
CONTAINER ID  BUILDER  IMAGE ID     IMAGE NAME                       CONTAINER NAME
332791d92fa5     *     b0c9d60fc5e3 docker.io/library/alpine:latest  alpine-working-container
nico@NICO-ASUS:/mnt/c/laragon/www/Exam_Docker$ buildah run alpine-working-container-1 apk add --no-cache curl
fetch https://dl-cdn.alpinelinux.org/alpine/v3.21/main/x86_64/APKINDEX.tar.gz
fetch https://dl-cdn.alpinelinux.org/alpine/v3.21/community/x86_64/APKINDEX.tar.gz
(1/9) Installing brotli-libs (1.1.0-r2)
(2/9) Installing c-ares (1.34.3-r0)
(3/9) Installing libunistring (1.2-r0)
(4/9) Installing libidn2 (2.3.7-r0)
(5/9) Installing nghttp2-libs (1.64.0-r0)
(6/9) Installing libpsl (0.21.5-r3)
(7/9) Installing zstd-libs (1.5.6-r2)
(8/9) Installing libcurl (8.11.1-r0)
(9/9) Installing curl (8.11.1-r0)
Executing busybox-1.37.0-r9.trigger
OK: 12 MiB in 24 packages
nico@NICO-ASUS:/mnt/c/laragon/www/Exam_Docker$ buildah commit alpine-working-container-1  my-custom-image:1.1
Getting image source signatures
Copying blob a0904247e36a skipped: already exists                                                                                                                     
Copying blob 320847198cbf done   |                                                                                                                                    
Copying config 146ac0386c done   |                                                                                                                                    
Writing manifest to image destination
146ac0386cfdef23aeaadeec27154b6259f9a79044ae0206669b6564870db70c
```

### 12. Copier des images avec Skopeo (100 pts)

**Objectif** : Copier une image Docker entre différents registres sans la télécharger.

**Explication** : Skopeo permet de manipuler des images Docker à distance, sans les télécharger localement.

Commande :

```sh
skopeo copy docker://nginx:latest docker://localhost:5000/nginx:latest --dest-tls-verify=false
skopeo list-tags docker://localhost:5000/nginx --tls-verify=false
```

**Réponse**:
```sh
nico@NICO-ASUS:/mnt/c/laragon/www/Exam_Docker$ skopeo copy docker://nginx:latest docker://localhost:5000/nginx:latest --dest-tls-verify=false

Getting image source signatures
Copying blob af302e5c37e9 done   |
Copying blob 9e9aab598f58 done   |
Copying blob 38e992d287c5 done   |
Copying blob 841e383b441e done   |
Copying blob 0256c04a8d84 done   |
Copying blob 207b812743af done   |
Copying blob 4de87b37f4ad done   |
Copying config 9bea9f2796 done   |                                                                                                                                    
Writing manifest to image destination
nico@NICO-ASUS:/mnt/c/laragon/www/Exam_Docker$ skopeo list-tags docker://localhost:5000/nginx --tls-verify=false
{
    "Repository": "localhost:5000/nginx",
    "Tags": [
        "latest"
    ]
}
```

### 13. Déployer une stack ELK avec Docker Compose (100 pts)

**Objectif** : Déployer une stack ELK (Elasticsearch, Logstash, Kibana) pour gérer les logs.

**Explication** : Une stack ELK centralise et analyse les logs des applications.

Fichiers présents dans le dossier `3-13`.

Commande :

```sh
docker-compose up -d
docker logs logstash
```

**Réponse** :

```sh
nico@NICO-ASUS:/mnt/c/laragon/www/Exam_Docker/3-13$ docker-compose up -d
WARN[0000] /mnt/c/laragon/www/Exam_Docker/3-13/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
[+] Running 35/3
 ✔ elasticsearch Pulled                                                                                                                                        542.5s 
 ✔ logstash Pulled                                                                                                                                             466.2s 
 ✔ kibana Pulled                                                                                                                                               336.7s 
[+] Running 5/5
 ✔ Network 3-13_elk                  Created                                                                                                                     0.3s 
 ✔ Volume "3-13_elasticsearch-data"  Created                                                                                                                     0.0s 
 ✔ Container elasticsearch2          Started                                                                                                                     1.4s 
 ✔ Container logstash                Started                                                                                                                     1.0s 
 ✔ Container kibana2                 Started                                                                                                                     1.1s 
 
```


### 14. Scanner des images Docker avec Trivy (100 pts)

**Objectif** : Scanner une image Docker à la recherche de vulnérabilités.

**Explication** : Trivy analyse les images pour détecter des failles de sécurité connues.

Commande :

```sh
trivy image nginx
```

**Réponse**:
```sh
nico@NICO-ASUS:/mnt/c/laragon/www/Exam_Docker$ trivy image nginx

2025-02-03T16:02:53+01:00       INFO    Vulnerability scanning is enabled
2025-02-03T16:02:53+01:00       INFO    Secret scanning is enabled
2025-02-03T16:02:53+01:00       INFO    If your scanning is slow, please try '--scanners vuln' to disable secret scanning
2025-02-03T16:02:53+01:00       INFO    Please see also https://aquasecurity.github.io/trivy/v0.52/docs/scanner/secret/#recommendation for faster secret detection    
2025-02-03T16:02:53+01:00       INFO    Detected OS     family="debian" version="12.9"
2025-02-03T16:02:53+01:00       INFO    [debian] Detecting vulnerabilities...   os_version="12" pkg_num=149
2025-02-03T16:02:53+01:00       INFO    Number of language-specific files       num=0

nginx (debian 12.9)

Total: 143 (UNKNOWN: 0, LOW: 95, MEDIUM: 35, HIGH: 11, CRITICAL: 2)

┌────────────────────┬─────────────────────┬──────────┬──────────────┬─────────────────────────┬───────────────┬──────────────────────────────────────────────────────────────┐
│      Library       │    Vulnerability    │ Severity │    Status    │    Installed Version    │ Fixed Version │                            Title                     
        │
├────────────────────┼─────────────────────┼──────────┼──────────────┼─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ apt                │ CVE-2011-3374       │ LOW      │ affected     │ 2.6.1                   │               │ It was found that apt-key in apt, all versions, do not       │
│                    │                     │          │              │                         │               │ correctly...                                         
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2011-3374            
        │
├────────────────────┼─────────────────────┤          │              ├─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ bash               │ TEMP-0841856-B18BAF │          │              │ 5.2.15-2+b7             │               │ [Privilege escalation possible to other user than root]      │
│                    │                     │          │              │                         │               │ https://security-tracker.debian.org/tracker/TEMP-0841856-B1- │
│                    │                     │          │              │                         │               │ 8BAF                                                 
        │
├────────────────────┼─────────────────────┤          │              ├─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ bsdutils           │ CVE-2022-0563       │          │              │ 1:2.38.1-5+deb12u3      │               │ util-linux: partial disclosure of arbitrary files in chfn    │
│                    │                     │          │              │                         │               │ and chsh when compiled...                            
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2022-0563            
        │
├────────────────────┼─────────────────────┤          ├──────────────┼─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ coreutils          │ CVE-2016-2781       │          │ will_not_fix │ 9.1-1                   │               │ coreutils: Non-privileged session can escape to the parent   │
│                    │                     │          │              │                         │               │ session in chroot                                    
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2016-2781            
        │
│                    ├─────────────────────┤          ├──────────────┤                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2017-18018      │          │ affected     │                         │               │ coreutils: race condition vulnerability in chown and chgrp   │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2017-18018           
        │
├────────────────────┼─────────────────────┼──────────┤              ├─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ curl               │ CVE-2024-11053      │ MEDIUM   │              │ 7.88.1-10+deb12u8       │               │ curl: curl netrc password leak                       
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2024-11053           
        │
│                    ├─────────────────────┤          │              │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2024-9681       │          │              │                         │               │ curl: HSTS subdomain overwrites parent cache entry   
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2024-9681            
        │
│                    ├─────────────────────┼──────────┤              │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2024-2379       │ LOW      │              │                         │               │ curl: QUIC certificate check bypass with wolfSSL     
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2024-2379            
        │
├────────────────────┼─────────────────────┤          │              ├─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ gcc-12-base        │ CVE-2022-27943      │          │              │ 12.2.0-14               │               │ binutils: libiberty/rust-demangle.c in GNU GCC 11.2 allows   │
│                    │                     │          │              │                         │               │ stack exhaustion in demangle_const                   
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2022-27943           
        │
│                    ├─────────────────────┤          │              │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-4039       │          │              │                         │               │ gcc: -fstack-protector fails to guard dynamic stack  
        │
│                    │                     │          │              │                         │               │ allocations on ARM64                                 
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2023-4039            
        │
├────────────────────┼─────────────────────┤          │              ├─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ gpgv               │ CVE-2022-3219       │          │              │ 2.2.40-1.1              │               │ gnupg: denial of service issue (resource consumption) using  │
│                    │                     │          │              │                         │               │ compressed packets                                   
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2022-3219            
        │
├────────────────────┼─────────────────────┼──────────┤              ├─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libaom3            │ CVE-2023-6879       │ CRITICAL │              │ 3.6.0-1+deb12u1         │               │ aom: heap-buffer-overflow on frame size change       
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2023-6879            
        │
│                    ├─────────────────────┼──────────┼──────────────┤                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2023-39616      │ HIGH     │ will_not_fix │                         │               │ AOMedia v3.0.0 to v3.5.0 was discovered to contain an
        │
│                    │                     │          │              │                         │               │ invalid read mem...                                  
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2023-39616           
        │
├────────────────────┼─────────────────────┼──────────┼──────────────┼─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libapt-pkg6.0      │ CVE-2011-3374       │ LOW      │ affected     │ 2.6.1                   │               │ It was found that apt-key in apt, all versions, do not       │
│                    │                     │          │              │                         │               │ correctly...                                         
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2011-3374            
        │
├────────────────────┼─────────────────────┤          │              ├─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libblkid1          │ CVE-2022-0563       │          │              │ 2.38.1-5+deb12u3        │               │ util-linux: partial disclosure of arbitrary files in chfn    │
│                    │                     │          │              │                         │               │ and chsh when compiled...                            
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2022-0563            
        │
├────────────────────┼─────────────────────┼──────────┤              ├─────────────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
│ libc-bin           │ CVE-2025-0395       │ MEDIUM   │              │ 2.36-9+deb12u9          │               │ glibc: buffer overflow in the GNU C Library's assert()       │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2025-0395            
        │
│                    ├─────────────────────┼──────────┤              │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2010-4756       │ LOW      │              │                         │               │ glibc: glob implementation can cause excessive CPU and       │
│                    │                     │          │              │                         │               │ memory consumption due to...                         
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2010-4756            
        │
│                    ├─────────────────────┤          │              │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2018-20796      │          │              │                         │               │ glibc: uncontrolled recursion in function            
        │
│                    │                     │          │              │                         │               │ check_dst_limits_calc_pos_1 in posix/regexec.c       
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2018-20796           
        │
│                    ├─────────────────────┤          │              │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010022    │          │              │                         │               │ glibc: stack guard protection bypass                 
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2019-1010022         
        │
│                    ├─────────────────────┤          │              │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010023    │          │              │                         │               │ glibc: running ldd on malicious ELF leads to code execution  │
│                    │                     │          │              │                         │               │ because of...                                        
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2019-1010023         
        │
│                    ├─────────────────────┤          │              │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010024    │          │              │                         │               │ glibc: ASLR bypass using cache of thread stack and heap      │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2019-1010024         
        │
│                    ├─────────────────────┤          │              │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010025    │          │              │                         │               │ glibc: information disclosure of heap addresses of   
        │
│                    │                     │          │              │                         │               │ pthread_created thread                               
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2019-1010025         
        │
│                    ├─────────────────────┤          │              │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-9192       │          │              │                         │               │ glibc: uncontrolled recursion in function            
        │
│                    │                     │          │              │                         │               │ check_dst_limits_calc_pos_1 in posix/regexec.c       
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2019-9192            
        │
├────────────────────┼─────────────────────┼──────────┤              │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│ libc6              │ CVE-2025-0395       │ MEDIUM   │              │                         │               │ glibc: buffer overflow in the GNU C Library's assert()       │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2025-0395            
        │
│                    ├─────────────────────┼──────────┤              │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2010-4756       │ LOW      │              │                         │               │ glibc: glob implementation can cause excessive CPU and       │
│                    │                     │          │              │                         │               │ memory consumption due to...                         
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2010-4756            
        │
│                    ├─────────────────────┤          │              │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2018-20796      │          │              │                         │               │ glibc: uncontrolled recursion in function            
        │
│                    │                     │          │              │                         │               │ check_dst_limits_calc_pos_1 in posix/regexec.c       
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2018-20796           
        │
│                    ├─────────────────────┤          │              │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010022    │          │              │                         │               │ glibc: stack guard protection bypass                 
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2019-1010022         
        │
│                    ├─────────────────────┤          │              │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010023    │          │              │                         │               │ glibc: running ldd on malicious ELF leads to code execution  │
│                    │                     │          │              │                         │               │ because of...                                        
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2019-1010023         
        │
│                    ├─────────────────────┤          │              │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010024    │          │              │                         │               │ glibc: ASLR bypass using cache of thread stack and heap      │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2019-1010024         
        │
│                    ├─────────────────────┤          │              │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
│                    │ CVE-2019-1010025    │          │              │                         │               │ glibc: information disclosure of heap addresses of   
        │
│                    │                     │          │              │                         │               │ pthread_created thread                               
        │
│                    │                     │          │              │                         │               │ https://avd.aquasec.com/nvd/cve-2019-1010025         
        │
│                    ├─────────────────────┤          │              │                         ├───────────────┼──────────────────────────────────────────────────────────────┤
```

## 🟣 Expert (500 points)

### 15. Construire une image en mode rootless (100 pts)

**Objectif** : Construire une image Docker en garantissant que les processus tournent sans droits root.

**Explication** : L’exécution des conteneurs avec un utilisateur non-root améliore la sécurité.

Présents dans le dossier `4-15`.


Commande :

```sh 
docker build -t rootless-container .
docker run --rm rootless-container
```

**Réponse**:
```sh
nico@NICO-ASUS:/mnt/c/laragon/www/Exam_Docker/4-15$ docker build -t rootless-container .

[+] Building 1.0s (10/10) FINISHED                                                                                                                     docker:default
 => [internal] load build definition from Dockerfile                                                                                                             0.0s
 => => transferring dockerfile: 252B                                                                                                                             0.0s
 => [internal] load metadata for docker.io/library/alpine:latest                                                                                                 0.0s
 => [internal] load .dockerignore                                                                                                                                0.0s
 => => transferring context: 2B                                                                                                                                  0.0s
 => CACHED [1/5] FROM docker.io/library/alpine:latest                                                                                                            0.0s
 => [internal] load build context                                                                                                                                0.0s
 => => transferring context: 141B                                                                                                                                0.0s
 => [2/5] RUN addgroup -S appgroup && adduser -S appuser -G appgroup                                                                                             0.5s 
 => [3/5] WORKDIR /app                                                                                                                                           0.0s
 => [4/5] COPY entrypoint.sh /app/entrypoint.sh                                                                                                                  0.0s
 => [5/5] RUN chmod +x /app/entrypoint.sh                                                                                                                        0.3s 
 => exporting to image                                                                                                                                           0.1s 
 => => exporting layers                                                                                                                                          0.0s 
 => => writing image sha256:29e485a3794a57275ca576c4641c96fd7282c4348c3fae481bcc8e5be6570d27                                                                     0.0s 
 => => naming to docker.io/library/rootless-container  
 
nico@NICO-ASUS:/mnt/c/laragon/www/Exam_Docker/4-15$ docker run --rm rootless-container
Lancement du conteneur avec l'utilisateur : appuser
```
### 16. Mettre en place un CI/CD avec Docker et GitHub Actions (100 pts)

**Objectif** : Automatiser le build et le déploiement d’une image Docker via GitHub Actions.

**Explication** : GitHub Actions permet d’automatiser la création et le déploiement d’images Docker à chaque push sur un dépôt.

Présent dans le fichier `.github/workflows/docker.yml`.

Le repo GitHub qui tourne les CI/CD figure [sur ce lien](https://github.com/x9nico/Exam_Docker)

Les releases sont déployées [sur ce lien](https://hub.docker.com/r/x9nico/exam_docker/tags).

La chaîne de CI/CD va créer une nouvelle version à chaque nouveaux commits une release sous le tag `master`.

La variable `DOCKER_PASSWORD` représente un PAT créé avec les droits `Read & Write` sur [Docker Hub](https://hub.docker.com)

**Réponse** : https://github.com/x9nico/Exam_Docker/actions/runs/13115081735/job/36587326563

### 17. Optimiser une image avec Dive (100 pts)

**Objectif** : Analyser la taille des couches d’une image Docker et l’optimiser.

**Explication** : Dive est un outil permettant d’analyser une image Docker couche par couche.

Commande :

````sh
docker run alpine:latest
dive alpine:latest
````

**Réponse** :
```sh
nico@NICO-ASUS:/mnt/c/laragon/www/Exam_Docker/4-15$ docker run alpine:latest
┃ ● Layers ┣━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ Permission     UID:GID       Size  Filetree                                        
Cmp   Size  Command                                                                drwxr-xr-x         0:0     809 kB  ├── bin                                         
                                                                                   -rwxrwxrwx         0:0        0 B  │   ├── arch → /bin/busybox                     
│ Layer Details ├───────────────────────────────────────────────────────────────── -rwxrwxrwx         0:0        0 B  │   ├── ash → /bin/busybox                      
                                                                                   -rwxrwxrwx         0:0        0 B  │   ├── base64 → /bin/busybox                   
                                                                                   -rwxrwxrwx         0:0        0 B  │   ├── bbconfig → /bin/busybox                 
│ Image Details ├───────────────────────────────────────────────────────────────── -rwxr-xr-x         0:0     809 kB  │   ├── busybox                                 
                                                                                   -rwxrwxrwx         0:0        0 B  │   ├── cat → /bin/busybox                      
                                                                                   -rwxrwxrwx         0:0        0 B  │   ├── chattr → /bin/busybox                   
                                                                                   -rwxrwxrwx         0:0        0 B  │   ├── chgrp → /bin/busybox   
```

### 18. Éviter les vulnérabilités avec Hadolint (100 pts)

**Objectif** : Vérifier un Dockerfile avec Hadolint et appliquer les meilleures pratiques.

**Explication** : Hadolint analyse les Dockerfiles pour détecter les mauvaises pratiques.

Commande :

```sh
cd 2-9
hadolint Dockerfile
```

**Réponse** :
```sh
nico@NICO-ASUS:/mnt/c/laragon/www/Exam_Docker$ cd 2-9
nico@NICO-ASUS:/mnt/c/laragon/www/Exam_Docker/2-9$ hadolint Dockerfile
Dockerfile:7 DL3059 info: Multiple consecutive `RUN` instructions. Consider consolidation.
```
