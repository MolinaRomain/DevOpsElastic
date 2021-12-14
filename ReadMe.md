# Projet DevOps

>Membres : Romain MOLINA, Dimitri ROMANO, François BONNIN

## Les outils utilisés
- VMWARE / VirtualBox
- Ubuntu
- Github

## Les technologies :
- Docker + Docker-compose : 
- ElasticSearch : logiciel utilisé pour l'indexation, la recherche et analyse de donnée
- Kibana : interface graphique pour de visualisatiosn des données elastic searchs
- FileBeats : agent de transfert de données ( devloppé par élasticsearch aussi )

## installation et choix
(il n'y a pas de version latest pour dockerhub)
docker pull elasticsearch:7.14.2
mais on peut utilisé les dépots officiels sans dockerhub :
    ''' docker.elastic.co/elasticsearch/elasticsearch:7.16.0 '''
Les versions d'elasticsearch,filebeats et kibana sont synchronisés, on a utilisé ici la version 7.16.0

Concernant elasticsearch on fonctionne en single node car c'est un exemple, l'utilisé de plusieurs node est purement sécuritaire car cela permet
d'augmenter la robuster de notre cluster ( élément constituer de plusieurs nodes qui garde les données et permet l'indexation).
Avec un node(virtual or physical host ), si celui si devient down alors le cluster sera down aussi.

On utilise docker-compose pour la simplicité de configuration 

    ''' docker kibana logs '''
Avant d'utiliser kibana il faut attendre la fin de la configuration du serveur, on peut donc utiliser cette commande

### 

chown root filebeat.yml
chmod go-w /usr/share/filebeat/filebeat.yml



