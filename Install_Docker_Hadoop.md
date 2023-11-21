**Sommaire**

[TOC]

# Installation de **Hadoop** via _Docker_

Les étapes pour installer **Hadoop** via _Docker_ sont largement adaptées de la page de [Lilia Sfaxi](https://insatunisia.github.io/TP-BigData/), elles-mêmes reposant sur le projet [github de Kai LIU](https://github.com/kiwenlau/Hadoop-cluster-docker).

---
## Installation de *Docker* et des nœuds

Pour installer *Docker*, merci de suivre les [consignes disponibles ici](https://docs.docker.com/desktop/), en fonction de votre système d'exploitation (lisez les _System requirements_ pour vérifier que votre machine est adaptée). Si votre machine est trop ancienne, ou avec peu d'espace disque ou mémoire RAM, il y a de bonnes chances que l'installation ne fonctionne pas. Si c'est le cas, 

- soit vous pouvez travailler avec votre voisin,    
- soit vous pouvez aller directement à la seconde partie du TP, et réaliser les exercices en local (sans **Hadoop**).

Nous allons utiliser tout au long de ce TP trois contenaires représentant respectivement un nœud maître (le _Namenode_) et deux nœuds esclaves (les _Datanodes_).

1. Depuis un _Terminal_, téléchargez l'image docker depuis [_dockerhub_](https://hub.docker.com) (volume à télécharger > 3.3 GB !). Si votre ordinateur est équipé de seulement 8GB de RAM, alors utilisez la commande suivante :
```bash
docker pull stephanederrode/docker-cluster-hadoop-spark-python-8:3.2
```
Sinon utilisez :
```bash
docker pull stephanederrode/docker-cluster-hadoop-spark-python-16:3.2
```

Ce container contient une distribution _Linux/Ubuntu_, et les librairies nécessaires pour utiliser **Hadoop** et **Spark**. Il contient également _Python3.x_ (version du langage _Python_ compatible avec les versions de **Hadoop** et **Spark** installées).

2. Créez les 3 contenaires à partir de l'image téléchargée. Pour cela:

a. Créez un réseau qui permettra de relier les trois contenaires:
```bash
docker network create --driver=bridge hadoop
```     

b. Créez et lancez les trois contenaires (les instructions `-p` permettent de faire un _mapping_ entre les ports de la machine hôte et ceux du contenaire). **Important** Dans la suite, adaptez la syntaxe `-16` à la syntaxe `-8`, en fonction de l'image que vous avez téléchargée.
```bash
docker run -itd --net=hadoop -p 9870:9870 -p 8088:8088 -p 7077:7077 \
  -p 16010:16010 -p 9999:9999 --name hadoop-master --hostname hadoop-master \
  stephanederrode/docker-cluster-hadoop-spark-python-16:3.2

docker run -itd -p 8040:8042 --net=hadoop --name hadoop-slave1 --hostname hadoop-slave1 \
  stephanederrode/docker-cluster-hadoop-spark-python-16:3.2

docker run -itd -p 8041:8042 --net=hadoop --name hadoop-slave2 --hostname hadoop-slave2 \
  stephanederrode/docker-cluster-hadoop-spark-python-16:3.2
```     


**Remarques** 

- Dans la commande précédente le caractère `\`, utilisé pour poursuivre une commande sur plusieurs lignes, peut parfois poser des pbs. Pour résoudre ce problème, vous pouvez copier cette ligne dans un éditeur de texte, et supprimer ce caractère ainsi que les sauts de ligne.

- Sur certaines machines, la première ligne de commande ne s'exécute pas correctement. L'erreur provient sans doute du port `9870` que doit déjà être utilisé par une autre application installée sur votre machine. Vous pouvez alors supprimer ce port de la première ligne de commande :
```bash
docker run -itd --net=hadoop -p 8088:8088 -p 7077:7077 -p 16010:16010 -p 9999:9999 \
  --name hadoop-master --hostname hadoop-master \
  stephanederrode/docker-cluster-hadoop-spark-python-16:3.2
```
 
- Le port `9999` sera utilisé dans la partie 3 de ce TP, au sujet de _Spark streaming_.

---
### Préparation au TP

- Entrez dans le contenaire `hadoop-master` pour commencer à l'utiliser
```bash
docker exec -it hadoop-master bash
```
Le résultat de cette exécution sera le suivant:
```bash
root@hadoop-master:~#
```
Il s'agit du `shell` ou du `bash` (_Linux/Ubuntu_) du nœud maître. 
  
- La commande `ls`, qui liste les fichiers et dossiers du dossier en cours, doit faire état des fichiers suivants :
```bash
hdfs start-hadoop.sh ventes
```
Le dossier _ventes_ contient un fichier _purchases.txt_ qui sera utilisé lors de la seconde partie du TP.

**Remarque** Ces étapes de configuration ne doivent être réalisées qu'une seule fois. Pour relancer le cluster (une fois qu'on a fermé et relancé son ordinateur p. ex.), il suffira 

1. de lancer l'application `Docker Desktop`, qui lance les _daemon Docker_.   
1. de lancer la commande suivante :
```bash
docker start hadoop-master hadoop-slave1 hadoop-slave2
```
Vous pouvez alors entrer dans le _Namenode_ :
```bash
docker exec -it hadoop-master bash
```