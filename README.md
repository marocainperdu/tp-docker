# Atelier docker - veille technologique

## **Procédure d'Installation de Docker Engine sur Ubuntu**



**Étape 1 : Désinstallation des Paquets Conflit**

Assurez-vous de désinstaller les versions précédentes de Docker ou des paquets similaires pour éviter les conflits :

```
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do
    sudo apt-get remove -y $pkg
done
```

**Étape 2 : Configuration du Dépôt Docker**

Ajoutez la clé GPG officielle de Docker et configurez le dépôt Docker dans votre système Ubuntu :

```
# Installation des prérequis
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common

# Ajout de la clé GPG Docker
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Configuration du dépôt Docker stable
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Mise à jour de l'index des paquets apt
sudo apt-get update
```

**Étape 3 : Installation de Docker Engine**

Installez Docker Engine, CLI, containerd et Docker Compose à l'aide des commandes suivantes :

```
# Installation de Docker Engine
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose
```

**Étape 4 : Vérification de l'Installation**

Vérifiez que Docker Engine est installé correctement en exécutant la commande :

```
sudo docker run hello-world
```

Vous devriez voir un message confirmant le succès de l'installation.

![Livrable 1](https://github.com/marocainperdu/tp-docker/blob/main/Livrable%201.png)


## **Ajout d'Utilisateurs Autorisés au Groupe Docker**


Le daemon Docker se lie à un socket Unix au lieu d'un port TCP. Par défaut, ce socket Unix appartient à l'utilisateur root et les autres utilisateurs ne peuvent y accéder qu'en utilisant sudo. Le daemon Docker s'exécute toujours en tant qu'utilisateur root.

Pour éviter d'avoir à utiliser sudo à chaque fois que vous lancez une commande Docker, créez un groupe Unix appelé docker et ajoutez-y des utilisateurs. Lorsque le daemon Docker démarre, il crée un socket Unix accessible par les membres du groupe docker.

### **Création du Groupe Docker :**

```bash
sudo groupadd docker
# Ceci pourrait déjà exister (depuis le programme d'installation du package)
```

### **Ajout de votre Utilisateur Actuel au Groupe Docker :**

```bash
sudo usermod -aG docker $USER
```

Déconnectez-vous et reconnectez-vous afin que votre appartenance au groupe soit réévaluée. Ou exécutez `newgrp docker` pour activer immédiatement les modifications apportées aux groupes.

Enfin, vérifiez que vous pouvez exécuter les commandes docker sans sudo :

```bash
docker --version
docker run hello-world
```
![Livrable 2](https://github.com/marocainperdu/tp-docker/blob/main/Livrable%202.png)

## **Conteneur : Installer et Configurer MySQL**



Téléchargez, configurez et exécutez le conteneur MySQL à l'aide des commandes suivantes :

### **Télécharger l'Image Officielle MySQL depuis DockerHub :**

```bash
docker pull mysql
```

### **Lancer l'Instance MySQL :**

```bash
docker run --name="MySQL" \
--network="bridge" \
--mount='type=volume,src=db,dst=/var/lib/mysql' \
-e MYSQL_ROOT_PASSWORD="Jacob" \
-e MYSQL_DATABASE="wordpress" \
-e MYSQL_USER="Jeanne" \
-e MYSQL_PASSWORD="Jacob" \
-d \
mysql:latest
```

- `--name` : Spécifie un nom convivial pour identifier le conteneur.
- `--network` : Spécifie le mode réseau du conteneur.
- `--mount` : Montre le stockage pour persister après l'arrêt du conteneur.
- `-e` : Définit des variables d'environnement spécifiques au conteneur.

Pour obtenir l'adresse IP interne assignée à l'image MySQL, utilisez la commande suivante :

```bash
docker inspect -f "{{ .NetworkSettings.IPAddress }}" MySQL
```



## **Conteneur : Installer et Configurer WordPress**



Téléchargez, configurez et exécutez le conteneur WordPress à l'aide des commandes suivantes :

### **Télécharger l'Image Officielle WordPress depuis DockerHub :**

```bash
docker pull wordpress
```

### **Lancer l'Instance WordPress :**

```bash
docker run --name="WordPress" \
--network="bridge" \
-p 80:80 \
--mount='type=volume,src=wordpress,dst=/var/www/html' \
-e WORDPRESS_DB_HOST="172.17.0.2" \
-e WORDPRESS_DB_USER=Jeanne \
-e WORDPRESS_DB_PASSWORD=Jacob \
-e WORDPRESS_DB_NAME=wordpress \
-e WORDPRESS_DEBUG=1 \
-d \
wordpress
```

- `--name` : Spécifie un nom convivial pour identifier le conteneur.
- `--network` : Spécifie le mode réseau du conteneur.
- `-p` : Publie les ports réseau du conteneur vers les ports réseau de l'hôte.
- `--mount` : Montre le stockage pour persister après l'arrêt du conteneur.
- `-e` : Définit des variables d'environnement spécifiques au conteneur.

Pour accéder à votre nouveau CMS WordPress depuis votre navigateur web, rendez-vous sur `http://localhost`.

![Livrable 3](https://github.com/marocainperdu/tp-docker/blob/main/Livrable%203%20bis%20.png)

![Livrable 3](https://github.com/marocainperdu/tp-docker/blob/main/Livrable%203.png)

### **Arrêter les Conteneurs :**

Pour arrêter les conteneurs MySQL et WordPress, utilisez la commande suivante :

```bash
docker stop MySQL WordPress
```



## **Surveillance et Commandes utiles avec Docker CLI**



Pour surveiller vos conteneurs Docker en cours d'exécution, vous pouvez utiliser la CLI Docker avec les commandes suivantes :

- Obtenir une liste des conteneurs en cours d'exécution :
  ```bash
  docker container ls
  ```

- Obtenir une liste de tous les conteneurs (y compris ceux arrêtés) :
  ```bash
  docker container ls --all
  ```

- Obtenir des informations détaillées sur un conteneur spécifique :
  ```bash
  docker container inspect CONTAINER_ID
  ```

- Obtenir des statistiques d'utilisation des ressources sur les conteneurs en cours d'exécution :
  ```bash
  docker container stats
  ```

- Afficher les processus en cours d'exécution dans un conteneur :
  ```bash
  docker top CONTAINER_ID
  ```

- Obtenir le mappage des ports pour un conteneur spécifique :
  ```bash
  docker port CONTAINER_ID
  ```

- Mettre en pause tous les processus en cours d'exécution dans un ou plusieurs conteneurs :
  ```bash
  docker pause CONTAINER_ID
  ```

  (Pour reprendre, utilisez `docker unpause CONTAINER_ID`)

- Arrêter un ou plusieurs conteneurs :
  ```bash
  docker stop CONTAINER_ID
  ```

- Supprimer un conteneur arrêté :
  ```bash
  docker rm CONTAINER_ID
  ```

- Afficher les volumes :
  ```bash
  docker volume ls
  ```

- Afficher des informations sur l'ensemble du système Docker :
  ```bash
  docker system info
  ```

- Afficher les images Docker disponibles sur le système local :
  ```bash
  docker images
  ```

  (Pour des images spécifiques, utilisez `docker images NAME:TAG`)

- Obtenir un shell interactif sur un conteneur en cours d'exécution :
  ```bash
  docker exec -it CONTAINER_ID /bin/bash
  ```

---

## Partie 5 – Créer un script de déploiement Docker Compose

Bien que la création de conteneurs Docker en ligne de commande fonctionne certainement, ce n'est pas le processus le plus facile à maintenir à grande échelle, en particulier lorsque plusieurs conteneurs doivent être lancés ensemble pour atteindre un objectif plus important. Docker Compose vous permet de définir une pile d'applications qui peut déployer plusieurs conteneurs à la fois.

Commencez par installer Docker Compose :

```bash
DOCKER_CONFIG=${DOCKER_CONFIG:-$HOME/.docker}
mkdir -p $DOCKER_CONFIG/cli-plugins
curl -SL https://github.com/docker/compose/releases/download/v2.17.2/docker-compose-linux-x86_64 -o $DOCKER_CONFIG/cli-plugins/docker-compose
chmod +x $DOCKER_CONFIG/cli-plugins/docker-compose
docker-compose --version  # Vérifiez l'installation
```

Créez un nouveau fichier `docker-compose.yml` sur votre système et définissez la spécification pour vos services MySQL et WordPress en utilisant YAML :

```yaml
version: '3.8'
services:
  db:
    image: mysql:latest
    restart: always
    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: Votre-utilisateur-WordPress
      MYSQL_PASSWORD: Mot-de-passe-pour-utilisateur-WordPress
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    volumes:
      - db:/var/lib/mysql

  wordpress:
    image: wordpress
    depends_on:
      - db
    restart: always
    ports:
      - "80:80"
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: Votre-utilisateur-WordPress
      WORDPRESS_DB_PASSWORD: Mot-de-passe-pour-utilisateur-WordPress
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wordpress:/var/www/html

volumes:
  db:
  wordpress:
```

Assurez-vous de remplacer `Votre-utilisateur-WordPress` et `Mot-de-passe-pour-utilisateur-WordPress` par vos propres valeurs.

Exécutez cette pile logicielle combinée avec Docker Compose :

```bash
docker-compose up -d
```

- L'option `-f` peut être utilisée pour spécifier un fichier YAML différent si nécessaire.
- L'option `-d` permet d'exécuter les conteneurs en mode détaché.

Vérifiez que votre pile logicielle fonctionne en accédant à WordPress dans votre navigateur à l'adresse `http://localhost`.

Pour arrêter et supprimer vos conteneurs, utilisez la commande suivante :

```bash
docker-compose down
```

Cette commande arrêtera les conteneurs associés à votre pile logicielle et supprimera les volumes Docker créés pour eux.

En suivant ces étapes, vous pourrez déployer et gérer facilement une pile logicielle combinant MySQL et WordPress à l'aide de Docker Compose. Assurez-vous de personnaliser les paramètres en fonction de vos besoins spécifiques.
