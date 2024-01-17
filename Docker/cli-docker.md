# Installer docker
```
sudo apt-get update
```
```
sudo apt install docker.io
```
```
sudo apt install docker-compose
```

## Nettoyer un peu docker

#### Élimination de toutes les images, conteneurs, volumes et réseaux inutilisés  
```
docker system prune -a
```

## Supprimer les images de Docker

#### Lister les images
```
docker images -a
```
#### Supprimer une ou plusieurs images spécifiques
```
docker rmi nom_image1 nom_image2 nom_image3 
```
#### Supprimer images non utilisées
```
docker images purge
```
#### Supprimer toutes les images
```
docker images -a
docker rmi $(docker images -a -q)
```


## Supprimer les containers

#### Lister les containers
```
docker ps -a
```
#### Supprimer un ou plusieurs containers spécifiques
```
docker rm nom_image1 nom_image2 nom_image3 
```
#### Exécuter et supprimer :
```
docker run --rm image_name
```
#### Supprimer les containers non utilisés
```
docker rm purge
```
#### Arrêter et supprimer tous les conteneurs
```
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```



```
docker stop $(docker ps -a -q)
```
```
docker rm $(docker ps -a -q)
```
```
docker rmi $(docker images -a -q)
```