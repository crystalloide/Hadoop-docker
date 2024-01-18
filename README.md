# Hadoop : installation lancement et utilisation dans gitpod


[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/crystalloide/Hadoop-docker)

## https://github.com/crystalloide/Hadoop-docker

## https://gitpod.io/workspaces

## https://hub.docker.com/r/apache/hadoop


# Apache Hadoop

Apache Hadoop est un framework qui permet le traitement distribué de grands ensembles de données sur des clusters d'ordinateurs,

à l'aide de modèles de programmation simples. 

Il est conçu pour passer d'un seul serveur à des milliers de machines, chacune offrant un calcul et un stockage locaux. 

Plutôt que de s'appuyer sur du matériel pour offrir une haute disponibilité, la bibliothèque elle-même est conçue pour détecter 

et gérer les pannes au niveau de la couche application, 

fournissant ainsi un service hautement disponible au-dessus d'un cluster d'ordinateurs, 

dont chacun peut être sujet à des pannes.

# Démarrage rapide

Un cluster Hadoop peut être créé en extrayant l'image Docker appropriée et en spécifiant les configurations requises.

# Exemple donc via docker : 

## Exemple de création de la dernière image hadoop-3 

- Créer tout d'abord un fichier docker-compose.yaml :

#### début du fichier yaml :

    
    version: "2"
    namenode:
          image: apache/hadoop:3
          hostname: namenode
          command: ["hdfs", "namenode"]
             - 9870:9870
          env_file:
             - ./config
          environment:
               ENSURE_NAMENODE_DIR: "/tmp/hadoop-root/dfs/name"
       datanode:
          image: apache/hadoop:3
          command: ["hdfs", "datanode"]
          env_file:
            - ./config
       resourcemanager:
          image: apache/hadoop:3
          hostname: resourcemanager
          command: ["yarn", "resourcemanager"]
          ports:
             - 8088:8088
          env_file:
            - ./config
          volumes:
            - ./test.sh:/opt/test.sh
       nodemanager:
          image: apache/hadoop:3
          command: ["yarn", "nodemanager"]
          env_file:
            - ./config
    

#### Fin du fichier yaml


docker pull apache/hadoop:latest

docker run --name mongodb -p 27017:27017 -d mongodb/mongodb-enterprise-server:latest

## Pour voir le conteneur lancé qui porte le moteur mongoDB : 

docker ps -a

## Pour arrêter l'instance lancée : 

docker stop mongodb

docker ps -a


# 2nde façon d'utiliser MongoDB : via instalaltion des packages : 

wget -qO- https://www.mongodb.org/static/pgp/server-7.0.asc | sudo tee /etc/apt/trusted.gpg.d/server-7.0.asc

sudo apt-get install gnupg

echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list

sudo apt-get update

sudo apt-get install -y mongodb-mongosh

sudo apt-get install -y mongodb-org

sudo apt-get install net-tools

mkdir data

mongod --dbpath /workspace/mongoDB/data 

mongosh

## Affichage de la version de mongoDB : 
## test> db.version();
## 7.0.4

show dbs;

use admin;

show collections;

use local;

show collections;

db.startup_log.find().limit(10);

db.startup_log.find().limit(10).pretty;


## Pour sortir du client shell mongosh : 
quit

## En adaptant en fonction du nom de votre workspace :  https://27017-crystalloide-mongodb-lygen7vizjf.ws-eu105.gitpod.io/

# Fin du TP
