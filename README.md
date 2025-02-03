# 🚀 Exercices Docker

## 🟢 Facile (400 points)

### 1. Lancer un conteneur simple (80 pts)
**Objectif** : Démarrer un conteneur à partir d’une image minimale et exécuter une commande.

**Explication** : Docker permet d’exécuter des applications dans des conteneurs isolés. Ici, nous utilisons une image ultra-légère, `alpine`, pour afficher un message.

**Commande** :
```sh
docker run alpine echo "Hello, Docker!"
```

### 2. Exposer un service web (80 pts)
**Objectif** : Lancer un conteneur Nginx et le rendre accessible depuis l’hôte sur un port spécifique.

**Explication** : Les conteneurs fonctionnent en isolation, mais vous pouvez les rendre accessibles via des ports mappés. L’image nginx contient un serveur HTTP.

Commande :

```sh
docker run -d -p 8080:80 nginx
```

### 3. Construire une image Docker personnalisée (80 pts)
**Objectif** : Créer une image contenant une page HTML statique et la tester.

**Explication** : Un Dockerfile permet de personnaliser une image. Vous allez copier une page HTML dans une image nginx et l’exécuter.

Voir fichier `1-3/Dockerfile` et `index.html`.

Commande (à la racine du projet) :

```sh
docker build -t custom-nginx .
docker run -d -p 8080:80 custom-nginx
```

### 4. Utiliser un volume persistant (80 pts)

**Objectif** : Stocker des fichiers persistants dans un volume pour ne pas perdre les données après l'arrêt du conteneur.

**Explication** : Par défaut, les données dans un conteneur sont perdues à sa suppression. Un volume Docker permet de stocker les fichiers même après l’arrêt.

Commande :

```sh
docker volume create mydata
docker run -d -v mydata:/data alpine sh -c "echo 'Données persistantes' > /data/file.txt"
docker run --rm -v mydata:/data alpine cat /data/file.txt
```

### 5. Utiliser une variable d’environnement (80 pts)
**Objectif** : Passer une variable d’environnement dans un conteneur et l’afficher.

**Explication** : Les applications conteneurisées peuvent recevoir des paramètres via des variables d’environnement.

Commande :

```sh
docker run --rm -e MESSAGE="Bonjour, Docker!" alpine sh -c 'echo $MESSAGE'
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

### 7. Construire et utiliser un Dockerfile multi-stage (120 pts)

**Objectif** : Réduire la taille d’une image en utilisant la construction multi-stage.

**Explication** : Un Dockerfile multi-stage compile une application dans un conteneur temporaire, puis copie uniquement les fichiers nécessaires.

Présents dans le dossier `2-7`.

Commande :

```sh
docker build -t myapp .
docker run --rm myapp
```

### 8. Configurer un Docker Compose (120 pts)

**Objectif** : Déployer une application avec plusieurs services en utilisant docker-compose.

**Explication** : `docker-compose` facilite le déploiement d’applications multi-conteneurs, comme une base de données et une API.

Présent dans le dossier `2-8`

Commande :

```sh
docker-compose up -d
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

## 🔴 Difficile (500 points)

### 10. Créer un registre Docker privé (100 pts)

**Objectif** : Héberger vos propres images Docker sur un registre privé.

**Explication** : Un registre Docker privé permet de stocker et distribuer vos propres images de manière sécurisée.

Commande :

```sh
docker run -d -p 5000:5000 --name registry2 registry:2
curl ttp://localhost:5000/v2/
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

### 12. Copier des images avec Skopeo (100 pts)

**Objectif** : Copier une image Docker entre différents registres sans la télécharger.

**Explication** : Skopeo permet de manipuler des images Docker à distance, sans les télécharger localement.

Commande :

```sh
skopeo copy docker://nginx:latest docker://localhost:5000/nginx:latest --dest-tls-verify=false
skopeo list-tags docker://localhost:5000/nginx --tls-verify=false
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

### 14. Scanner des images Docker avec Trivy (100 pts)

**Objectif** : Scanner une image Docker à la recherche de vulnérabilités.

**Explication** : Trivy analyse les images pour détecter des failles de sécurité connues.

Commande :

```sh
trivy image nginx
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

### 16. Mettre en place un CI/CD avec Docker et GitHub Actions (100 pts)

**Objectif** : Automatiser le build et le déploiement d’une image Docker via GitHub Actions.

**Explication** : GitHub Actions permet d’automatiser la création et le déploiement d’images Docker à chaque push sur un dépôt.

Présent dans le fichier `.github/workflows/docker.yml`.

Le repo GitHub qui tourne les CI/CD figure [sur ce lien](https://github.com/x9nico/Exam_Docker)

Les releases sont déployées [sur ce lien](https://hub.docker.com/r/x9nico/exam_docker/tags).

La chaîne de CI/CD va créer une nouvelle version à chaque nouveaux commits une release sous le tag `master`.

La variable `DOCKER_PASSWORD` représente un PAT créé avec les droits `Read & Write` sur [Docker Hub](https://hub.docker.com)

### 17. Optimiser une image avec Dive (100 pts)

**Objectif** : Analyser la taille des couches d’une image Docker et l’optimiser.

**Explication** : Dive est un outil permettant d’analyser une image Docker couche par couche.

Commande :

````sh
docker run alpine:latest
dive alpine:latest
````

### 18. Éviter les vulnérabilités avec Hadolint (100 pts)

**Objectif** : Vérifier un Dockerfile avec Hadolint et appliquer les meilleures pratiques.

**Explication** : Hadolint analyse les Dockerfiles pour détecter les mauvaises pratiques.

Commande :

```sh
cd 2-9
hadolint Dockerfile
```