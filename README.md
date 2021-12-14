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

## Formation du docker-compose
### elastic search service :
'''
elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.16.0
    container_name: elasticsearch
    environment:
      - node.name=elasticsearch
      - cluster.name=es-cluster-7
      - discovery.type=single-node
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - data:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - es-network
'''
- image officiel
- environment :
    - nom du node
    - nom du cluster associé
    - discovery.type : single ou multiple pour la formation du cluster
    - ES_JAVA_OPTS : configurer JVM 
        -xms et xms pour configurer la taille de du tas de la JVM, basé sur la ram, il faut au moins 512 dans notre cas sinon elastics search 
            aura l'exeption OutOfMemory avec filebeats qui toune en même temps
    - memlock : éviter le swapping de la mémoire par le node
    - volume pour les datas d'elasticsearch 
    - network : sera la même pour nos trois services 

    
chown root filebeat.yml
chmod go-w /usr/share/filebeat/filebeat.yml



