Déploiement de Docker WordPress en Production
 5 mins  Installer WordPress avec Docker
Ce tutoriel fournit un guide pratique pour le déploiement de WordPress en production avec Docker. Vous apprendrez comment déployer automatiquement WordPress en ligne en utilisant docker-compose.

Le kit d’installation comprend:

Serveur Nginx, reverse-proxy et letsencrypt pour les certificats HTTPS
Fichiers WordPress
MariaDB/MySQL utilisé pour la base de données WordPress
Interface phpMyAdmin pour se connecter à votre base de données MySQL
WP-Cli : Interface de ligne de commande WordPress
Directives Makefile pour l’automatisation.


# Prérequis
* Lisez ceci : WordPress Local Development Using Docker Compose
* Lisez aussi ceci : Comment héberger plusieurs sites web HTTPS sur un seul serveur
* Git, docker et docker-compose sont installés sur votre serveur
* Votre hôte doit être accessible au public sur les deux ports 80 and 443. Vérifiez les règles de votre pare-feu pour vous assurer que ces ports sont ouverts.
* Dans ce tutoriel, nous supposerons que le répertoire Web se trouve à /srv/www
* Créer des domaines et sous-domaines:
* Un pour votre site WordPress: www.example.com
* Un pour accéder à votre base de données wordpress via phpMyadmin: sql.example.com


# Étape 1. Créer des répertoires de sites web**
En savoir plus : Créer un répertoire de sites web et définir les autorisations appropriées

#### 0. Paramètres varaibles
```
web_dir=/srv/www
myusername=kassambara
```
#### 1. Créer le répertoire des sites web
```
sudo mkdir -p $web_dir
```
#### 2. définir votre utilisateur comme propriétaire
```
sudo chown -R $myusername $web_dir
```
#### 3. définir le serveur web comme propriétaire du groupe
```
sudo chgrp -R www-data $web_dir
```
#### 4. Autorisations 755 pour tout
```
sudo chmod -R 755 $web_dir
```
#### 5. Les nouveaux fichiers et dossiers héritent 

##### Propriété du groupe à partir du dossier parent
```
chmod g+s $web_dir
```

# Étape 2. Télécharger un modèle de serveur web nginx-proxy pour l’hébergement de plusieurs sites web sur le même serveur

#### Télécharger un modèle de proxy nginx
```
web_dir=/srv/www
git clone https://github.com/kassambara/nginx-multiple-https-websites-on-one-server $web_dir
```
#### Mise à jour de nginx.tmpl : modèle de fichier de configuration Nginx
```
rm -rf $web_dir/nginx-proxy/nginx.tmpl
curl -s https://raw.githubusercontent.com/jwilder/nginx-proxy/master/nginx.tmpl> $web_dir/nginx-proxy/nginx.tmpl
```
#### Supprimer les fichiers et dossiers inutiles
```
cd $web_dir
rm -rf your-website-one.com your-website-two.com README.Rmd .gitignore .Rbuildignore .git
```
Le dossier nginx-proxy téléchargé contient un fichier docker-compose.yml. Trois services seront ainsi lancés:
- nginx: le proxy nginx-reverse, utilise l’image nginx par défaut. Le label est nécessaire pour que le conteneur letencrypt sache quel conteneur proxy nginx utiliser.
- nginx-gen: utilise l’image jwilder/docker-gen. Son instruction de commande rendra une configuration nginx (basée sur nginx.tmpl) pour chaque site web / conteneur ajouté au réseau.
- nginx-letsencrypt: génère et renouvelle les certificats HTTPS. En savoir plus : Comment héberger plusieurs sites web HTTPS sur un seul serveur.
Tous ces services sont liés au réseau nginx-proxy.

# Étape 3. Téléchargez un modèle de docker-compose WordPress
Téléchargez un modèle à partir de Github à l’adresse kassambara/wordpress-docker-compose

#### Téléchargez un modèle de docker-compose WordPress et le placer dans un dossier portant le même nom que votre nom de domaine
```
your_domain_name=www.example.com
cd $web_dir
git clone https://github.com/DavidBeraud/wordpress-docker-compose $your_domain_name
```

# Changer le nom du fichier docker-compose-onlinehost
```
cd $your_domain_name
mv docker-compose-onlinehost.yml docker-compose.yml
```

# Supprimer les fichiers et dossiers inutiles
```
rm -rf .git .gitignore
Modèle d’arborescence de répertoires:
```

www/www.example.com
├── LICENSE
├── Makefile
├── README.md
├── config
│   └── php.conf.ini
├── docker-compose.yml
├── mysql
├── setup-onlinehost.sh
├── wordpress
├── wp-auto-config.yml
└── wpcli
    ├── Dockerfile
    ├── Makefile
    └── entrypoint.sh

Dossiers essentiels:
- mysql : Fichiers de base de données MySQL pour MariaDB
- wordpress : Fichiers web WordPress
- wpcli contient un exemple de Dockerfile pour construire le CLI de WordPress.
- setup-onlinehost.sh : Définition automatique des variables de l’environnement de production en ligne.
- Fichier .env : contient les variables d’environnement requises pour l’installation de wordpress
- docker-compose.yml : docker-compose définissant les services WordPress
- Makefile : Ensemble de lignes de commande bash simples pour construire, installer et configurer WordPress, ainsi que pour démarrer et arrêter les conteneurs dockers.

# Étape 4. Inspectez votre annuaire web

Votre répertoire web devrait ressembler à ceci:

www
├── README.md
├── nginx-proxy
│   ├── certs
│   ├── conf.d
│   ├── docker-compose.yml
│   ├── html
│   ├── nginx.tmpl
│   └── vhost.d
└── www.example.com
    ├── LICENSE
    ├── Makefile
    ├── README.md
    ├── config
    ├── docker-compose.yml
    ├── mysql
    ├── setup-onlinehost.sh
    ├── wordpress
    ├── wp-auto-config.yml
    └── wpcli

# Étape 5. Modifier les variables d’environnement de configuration du Docker WordPress
#### 1. Ouvrez le fichier d'installation
```
nano $web_dir/$your_domain_name/setup-onlinehost.sh
```
# 2. Modifiez ces paramètres comme vous le souhaitez 
```
project_name="wordpress"
user_name="wordpress"
pass_word="wordpress"
email="your-email@example.com"
website_title="My Blog"
website_url="https://www.example.com"
phmyadmin_url="sql.example.com"
env_file=".env"
compose_file="docker-compose.yml"
```
# 3. Exécuter : 
```
chmod +x setup-onlinehost.sh && ./setup-onlinehost.sh 
```
Un fichier .env automatiquement mis à jour est disponible pour définir facilement les variables de docker-compose sans avoir à trop modifier le fichier de configuration docker-compose.yml lui-même.

Ouvrez le fichier .env et mettez le contenu à jour si vous le souhaitez. Dans notre exemple, le dossier se trouve à www/www.example.com/.env

# Ouvrir le dossier
```
nano $web_dir/$your_domain_name/.env
```

Contenu:

#### 1/ Nom de Project -------------------------------------------------
Doit être en minuscules, sans espaces et sans caractères de chemins invalides.
Sera également utilisé comme nom de la base de données de WP
```
COMPOSE_PROJECT_NAME=wordpress
```
####  2/ Base de données login et mot de passe -----------------------------------------
Définir un utilisateur de la base de données non root si vous le souhaitez (facultatif)
```
DATABASE_PASSWORD=password
DATABASE_USER=root
```
#### 3/ Pour l'autoinstallation et l'auto-configuration WordPress-------------------
```
WORDPRESS_WEBSITE_TITLE="My Blog"
```
#### URL : Utilisez ceci pour localhost
```
WORDPRESS_WEBSITE_URL="https://www.example.com"
WORDPRESS_WEBSITE_URL_WITHOUT_HTTP="www.example.com"
WORDPRESS_WEBSITE_POST_URL_STRUCTURE="/blog/%postname%/"
```
#### Identification de l'administrateur du site web. Précisez un mot de passe fort 
```
WORDPRESS_ADMIN_USER="wordpress"
WORDPRESS_ADMIN_PASSWORD="wordpress"
WORDPRESS_ADMIN_EMAIL="your-email@example.com"
```
#### 4/ Versions des logiciels-----------------------------------------------
```
WORDPRESS_VERSION=latest
MARIADB_VERSION=latest
```
#### 5/ Ports: Peut être changés -------------------------------------------
```
PHPMYADMIN_PORT=8080
```
#### 6/ Volumes sur l'hôte--------------------------------------------------
```
WORDPRESS_DATA_DIR=./wordpress
```
#### 7/ Vérification de la disponibilité des services d'hébergement (serveur mysql et woordpress)
###### Temps d'attente en seconde
```
WAIT_BEFORE_HOSTS=5
WAIT_AFTER_HOSTS=5
WAIT_HOSTS_TIMEOUT=300
WAIT_SLEEP_INTERVAL=60
WAIT_HOST_CONNECT_TIMEOUT=5
```
#### 8/ Utilisé seulement pour le déploiement en ligne -------------------------------
```
WORDPRESS_WEBSITE_URL_WITHOUT_WWW=example.com
PHPMYADMIN_WEBSITE_URL_WITHOUT_HTTP=sql.example.com
```
# Étape 6. Éditer le fichier de configuration docker-compose
Inspectez et mettez à jour le fichier $web_dir/$your_domain_name/docker-compose.yml si vous le souhaitez. Il contient un conteneur qui redirige les URL non-www vers www (recommandé). Si vous ne voulez pas de ce comportement, il suffit de retirer le conteneur.

#### Rediriger les URL non-www vers www
```
  redirectnonwww:
    image: cusspvz/redirect
    container_name: ${COMPOSE_PROJECT_NAME}_redirectnonwww
    restart: always
    environment:
      - VIRTUAL_HOST=${WORDPRESS_WEBSITE_URL_WITHOUT_WWW}
      - HTTPS_METHOD=noredirect
      - LETSENCRYPT_HOST=${WORDPRESS_WEBSITE_URL_WITHOUT_WWW}
      - LETSENCRYPT_EMAIL=${WORDPRESS_ADMIN_EMAIL}
      - REDIRECT=${WORDPRESS_WEBSITE_URL}
      # CHANGER L'URL DE REDIRECTION
      - REDIRECT=https://www.example.com
      - WORKER_CONNECTIONS=1024
    networks:
      - ${COMPOSE_PROJECT_NAME}_default
      - nginx-proxy
```
# Étape 7. Exécutez le reverse proxy nginx
#### 1. Créer le network docker. Faites-le une fois
```
docker network create nginx-proxy
```
#### 2. Créez le reverse-proxy avec le 
#####  conteneurs nginx, nginx-gen et nginx-letsencrypt
```
cd $web_dir/nginx-proxy/
docker-compose up -d
```
# Étape 8. Permettre aux utilisateurs de poster des documents ou des images de grande taille
Dans le fichier de configuration Nginx, vous devez également augmenter l’option client_max_body_size pour permettre aux utilisateurs de poster des documents volumineux. Vous pouvez par exemple ajouter la ligne client_max_body_size 64M; dans la directive server{}.

#### 1. Créer un fichier de configuration pour votre domaine dans le dossier vhost de nginx-proxy
```
nano $web_dir/nginx-proxy/vhost.d/$your_domain_name
```
#### 2. Ajoutez ceci pour permettre le téléchargement de 64M
```
client_max_body_size 64M;
```
###### Une seule commande pour effectuer l’opération ci-dessus:
```
echo 'client_max_body_size 64M;' > nano $web_dir/nginx-proxy/vhost.d/$your_domain_name
```
# Étape 9. Installer WordPress en utilisant docker-compose
Deux méthodes sont disponibles pour installer et configurer votre site web WordPress:

- Installation automatique + configuration manuelle
- Installation automatique + configuration automatique

##### Méthode 1. Installation automatique + configuration manuelle
###### Installation automatique de WordPress dans docker
Trois lignes de commandes docker-compose:
Construire des images d’applications docker
Démarrer l’installation de wordpress en mode détaché
Vérifier la disponibilité des services de docker WordPress
Allez dans le dossier de votre nom de domaine et tapez ceci:
```
cd $your_domain_name
docker-compose build
docker-compose up -d 
docker-compose run --rm healthcheck
```
##### Expliquer les différentes options de docker-compose:
- -d: démarrer des conteneurs en option détachée
- --rm: retirer les conteneurs lorsqu’ils sont arrêtés

 Vous pouvez également utiliser les commandes de raccourci suivantes de make. Facile à utiliser sur les systèmes d’exploitation Unix (MAC et Linux)

```
cd $your_domain_name
make install
```
Les journaux de la console ressemblent à ceci:
Log de la console
Configuration manuelle de WordPress
Naviguez sur votre navigateur à l’adresse www.example.com et suivez les instructions d’installation
Définir la langue de WordPress
Sélectionnez la langue de WordPress
Créer un utilisateur administratif
Créer un utilisateur administratif
Succès
Succès de l’installation
Connectez-vous en tant qu’utilisateur administratif
Connectez-vous en tant qu’admin
Tableau de bord de l’administration
Tableau de bord de l’administration

##### Méthode 2. Installation et configuration automatiques
Avant de réessayer l’installation, il faut d’abord tout nettoyer comme suit:
```
docker-compose down
rm -rf mysql/* wordpress/*
```
Les commandes suivantes de docker-compose ou de make peuvent être utilisées pour installer et configurer automatiquement wordpress.

#### Créer des images dockers et lancer wordpress
```
docker-compose up -d --build
```
#### Configuration automatique de wordpress
```
docker-compose -f docker-compose.yml -f wp-auto-config.yml run --rm wp-auto-config
```
#### Utilisation des commandes make. Pour les utilisateurs de systèmes Unix (MAC et Linux):
```
make autoinstall
```

# Étape 10. Accès à votre site web
Visitez votre site web wordpress à l’adresse www.example.com. Identification par défaut pour admin (www.example.com/wp-login.php):
Username: wordpress
Password: wordpress
Site internet

Une fois votre site en ligne, vous pouvez commencer à créer et à publier le contenu que vous souhaitez dans votre instance WordPress.

Visitez votre base de données via phpMyAdmin à l’adresse http://sql.example.com
Username: root
Password: password
phpMyAdmin

#### Arrêt et nettoyage
Cette section présente un ensemble de commandes utiles à connaître.
Fermeture des conteneurs et des dépendances du docker WordPress. La commande docker-composer down supprime les conteneurs et le réseau par défaut, mais préserve votre base de données WordPress.

#### Arrêter et supprimer les conteneurs
```
docker-compose down
```
#### (Re)démarrer les conteneurs docker-compose de WordPress
```
docker-compose up -d
```
#### Réinitialisez tout
Arrêter et supprimer les conteneurs
Supprimer les fichiers wordpress et mysql installés
```
docker-compose down
rm -rf mysql/* wordpress/*
```
Notez qu’au lieu d’utiliser les commandes docker-compose ci-dessus, vous pouvez aussi utiliser facilement les lignes de commande de raccourcis make suivantes si vous avez des systèmes Unix (MAC / Linux).

#### Construire et démarrer le site wordpress
```
make start
```
#### Arrêtez et supprimez les conteneurs docker de WordPress
```
make down
```
#### Tout réinitialiser
```
make reset
```
