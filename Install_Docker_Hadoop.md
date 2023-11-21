**Sommaire**

[TOC]
# Objectifs du TP
Initiation au framework hadoop et utilisation de docker pour lancer un cluster hadoop de 3 noeuds.
# Outils et Versions
- Apache Hadoop Version : 2.7.2.
- Docker Version 17.09.1
- IntelliJ IDEA Version Ultimate 2016.1 (ou tout autre IDE de votre choix)
- Java Version 1.8.
- Unix-like ou Unix-based Systems (Divers Linux et MacOS)

# Installation de *Docker*

Pour installer *Docker*, merci de suivre les [consignes disponibles ici](https://docs.docker.com/desktop/), en fonction de votre système d'exploitation (lisez les _System requirements_ pour vérifier que votre machine est adaptée). Si votre machine est trop ancienne, ou avec peu d'espace disque ou mémoire RAM, il y a de bonnes chances que l'installation ne fonctionne pas. Si c'est le cas, 

# Installation de **Hadoop** via _Docker_

---
Nous allons utiliser tout au long de ce TP trois contenaires représentant respectivement un nœud maître (Namenode) et deux nœuds esclaves (Datanodes).
1. Depuis un _Terminal_, téléchargez l'image docker depuis [_dockerhub_](https://hub.docker.com) en utilisant la commande suivante :

```bash
docker pull melossmani/hadoop-spark:um6p
```


Ce container contient une distribution _Linux/Ubuntu_, et les librairies nécessaires pour utiliser **Hadoop** et **Spark**. Il contient également _Python3.x_ (version du langage _Python_ compatible avec les versions de **Hadoop** et **Spark** installées).

2. Créez les 3 contenaires à partir de l'image téléchargée. Pour cela :
  
   a. Créez un réseau qui permettra de relier les trois contenaires :
```bash
   docker network create --driver=bridge hadoop
```     
   b. Créez et lancez les trois contenaires (les instructions `-p` permettent de faire un _mapping_ entre les ports de la machine hôte et ceux du contenaire). 
   ```bash
     docker run -itd --net=hadoop -p 50070:50070 -p 8088:8088 -p 7077:7077 \
     -p 16010:16010 --name hadoop-master --hostname hadoop-master \
      melossmani/hadoop-spark:um6p
   ```

   ```bash
     docker run -itd -p 8040:8042 --net=hadoop --name hadoop-slave1 --hostname hadoop-slave1 \
     melossmani/hadoop-spark:um6p
   ```

   ```bash
     docker run -itd -p 8041:8042 --net=hadoop --name hadoop-slave2 --hostname hadoop-slave2 \
     melossmani/hadoop-spark:um6p
   ```     


**Remarques** 

- Dans la commande précédente le caractère `\`, utilisé pour poursuivre une commande sur plusieurs lignes, peut parfois poser des pbs. Pour résoudre ce problème, vous pouvez copier cette ligne dans un éditeur de texte, et supprimer ce caractère ainsi que les sauts de ligne.
 
- Le port `9999` sera utilisé dans le TP2, au sujet de _Spark streaming
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
Il s'agit du `shell` ou du `bash` (_Linux/Ubuntu_) du nœud maître. Vous vous retrouverez dans le shell du namenode, et vous pourrez ainsi manipuler le cluster à votre
guise. La première chose à faire, une fois dans le contenaire, est de lancer hadoop et yarn. Un script est
fourni pour cela, appelé start-hadoop.sh. Lancer ce script.
```bash
./start-hadoop.sh
```
Le résultat devra ressembler à ce qui suit :



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
