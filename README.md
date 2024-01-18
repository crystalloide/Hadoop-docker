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

- On crée tout d'abord le fichier docker-compose.yaml :

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

### Note : On modifie la version de l'image en remplaçant "apache/hadoop:3" par "apache/hadoop:3.3.5" pour utiliser l'image Apache Hadoop 3.3.5

- On crée ensuite le fichier de configuration nommé "config" :
  
#### Début du fichier de configuration : 

    CORE-SITE.XML_fs.default.name=hdfs://namenode
    CORE-SITE.XML_fs.defaultFS=hdfs://namenode
    HDFS-SITE.XML_dfs.namenode.rpc-address=namenode:8020
    HDFS-SITE.XML_dfs.replication=1
    MAPRED-SITE.XML_mapreduce.framework.name=yarn
    MAPRED-SITE.XML_yarn.app.mapreduce.am.env=HADOOP_MAPRED_HOME=$HADOOP_HOME
    MAPRED-SITE.XML_mapreduce.map.env=HADOOP_MAPRED_HOME=$HADOOP_HOME
    MAPRED-SITE.XML_mapreduce.reduce.env=HADOOP_MAPRED_HOME=$HADOOP_HOME
    YARN-SITE.XML_yarn.resourcemanager.hostname=resourcemanager
    YARN-SITE.XML_yarn.nodemanager.pmem-check-enabled=false
    YARN-SITE.XML_yarn.nodemanager.delete.debug-delay-sec=600
    YARN-SITE.XML_yarn.nodemanager.vmem-check-enabled=false
    YARN-SITE.XML_yarn.nodemanager.aux-services=mapreduce_shuffle
    CAPACITY-SCHEDULER.XML_yarn.scheduler.capacity.maximum-applications=10000
    CAPACITY-SCHEDULER.XML_yarn.scheduler.capacity.maximum-am-resource-percent=0.1
    CAPACITY-SCHEDULER.XML_yarn.scheduler.capacity.resource-calculator=org.apache.hadoop.yarn.util.resource.DefaultResourceCalculator
    CAPACITY-SCHEDULER.XML_yarn.scheduler.capacity.root.queues=default
    CAPACITY-SCHEDULER.XML_yarn.scheduler.capacity.root.default.capacity=100
    CAPACITY-SCHEDULER.XML_yarn.scheduler.capacity.root.default.user-limit-factor=1
    CAPACITY-SCHEDULER.XML_yarn.scheduler.capacity.root.default.maximum-capacity=100
    CAPACITY-SCHEDULER.XML_yarn.scheduler.capacity.root.default.state=RUNNING
    CAPACITY-SCHEDULER.XML_yarn.scheduler.capacity.root.default.acl_submit_applications=*
    CAPACITY-SCHEDULER.XML_yarn.scheduler.capacity.root.default.acl_administer_queue=*
    CAPACITY-SCHEDULER.XML_yarn.scheduler.capacity.node-locality-delay=40
    CAPACITY-SCHEDULER.XML_yarn.scheduler.capacity.queue-mappings=
    CAPACITY-SCHEDULER.XML_yarn.scheduler.capacity.queue-mappings-override.enable=false

#### Fin du fichier de configuration

### Note :  on peut modifier et attribuer n'importe quelle nouvelle configuration dans un format similaire dans ce fichier.

### On vérifier la présenc edes ficheirs précédents dans le répertoire actuel :

    ls -l

#### Affichage : 

    -rw-r--r--  1 hadoop  apache  2547 Jun 23 15:53 config
    -rw-r--r--  1 hadoop  apache  1533 Jun 23 16:07 docker-compose.yaml


### Lancement des conteneurs Docker avec docker compose :

docker compose up -d

#### Affichage : 

    Creating network "docker-3_default" with the default driver
    Creating docker-3_namenode_1        ... done
    Creating docker-3_datanode_1        ... done
    Creating docker-3_nodemanager_1     ... done
    Creating docker-3_resourcemanager_1 ... done

#### Affichage des conteneurs lancés : 

    gitpod /workspace/Hadoop-docker (main) $ docker ps -a
    CONTAINER ID   IMAGE             COMMAND                  CREATED          STATUS          PORTS                                       NAMES
    0e61cf32a87f   apache/hadoop:3   "/usr/local/bin/dumb…"   41 seconds ago   Up 39 seconds   0.0.0.0:9870->9870/tcp, :::9870->9870/tcp   hadoop-docker-namenode-1
    48d4fc88a505   apache/hadoop:3   "/usr/local/bin/dumb…"   41 seconds ago   Up 40 seconds                                               hadoop-docker-nodemanager-1
    961e25bf5067   apache/hadoop:3   "/usr/local/bin/dumb…"   41 seconds ago   Up 39 seconds   0.0.0.0:8088->8088/tcp, :::8088->8088/tcp   hadoop-docker-resourcemanager-1
    0dc8c4eb8516   apache/hadoop:3   "/usr/local/bin/dumb…"   41 seconds ago   Up 39 seconds                                               hadoop-docker-datanode-1


### Accès au cluster :

#### On se connecte à un nœud :

#### On peut se connecter à n'importe quel nœud en spécifiant le conteneur :

    docker exec -it docker-3_namenode_1 /bin/bash 

#### Exécuter un exemple de job (ici "Pi") :

    yarn jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.3.5.jar pi 10 15

#### Ce qui précède exécutera un job de calcul de Pi

####  Toute commande Hadoop pourra être exécutée en suivant la même méthode

### Accès à l'interface utilisateur : 

#### L'interface utilisateur de Namenode (HDFS) est accessible à l'adresse http://localhost:9870/ 

#### et l'interface utilisateur de ResourceManager (Yarn/MR) est accessible à l'adresse http://localhost:8088/

### Arrêt du cluster : 
#### Le cluster peut être arrêté avec la commande suivante :
    docker compose down
    
Note:

L'exemple ci-dessus concerne la ligne Hadoop-3.x. 

Si vous souhaitez créer Hadoop-2.x, les étapes similaires mais les fichiers docker-compose.yaml et config sont différents.

Voir ici : https://github.com/apache/hadoop/tree/docker-hadoop-2


Code source du Docker :

Les images Docker sont créées via des branches et le code source 

- de la branche 3 se trouve à https://github.com/apache/hadoop/tree/docker-hadoop-3
  
- et pour la branche 2 à https://github.com/apache/hadoop/tree/docker-hadoop-2
  

Pour contacter les développeurs Hadoop : https://hadoop.apache.org/mailing_lists.html

Lectures complémentaires : https://hadoop.apache.org/


# Fin du TP
