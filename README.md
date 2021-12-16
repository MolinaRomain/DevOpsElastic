# Projet DevOps

>Membres : Romain MOLINA, Dimitri ROMANO, François BONNIN

Le but ici est d'utiliser Elastic search dans le cadre du monitoring de logs
pour cet exemple on utilisera les logs fournis par docker, mais l'application de se tuto est valable pour une application personnel ou tout autre
logiciel qui fournit des logs

## Les outils utilisés
- VMWARE / VirtualBox
- Ubuntu
- Github

## Les technologies :
- Docker + Docker-compose 
- ElasticSearch : logiciel utilisé pour l'indexation, la recherche et analyse de donnée
- Kibana : interface graphique pour de visualisatiosn des données elastic searchs
- FileBeats : agent de transfert de données ( devloppé par élasticsearch aussi )

## installation et choix
(il n'y a pas de version latest pour dockerhub)
docker pull elasticsearch:7.14.2
mais on peut utilisé les dépots officiels sans dockerhub :
    ``` 
    docker.elastic.co/elasticsearch/elasticsearch:7.16.0 
    ```
Les versions d'elasticsearch,filebeats et kibana sont synchronisés, on a utilisé ici la version 7.16.0

Concernant elasticsearch on fonctionne en single node car c'est un exemple, l'utilisé de plusieurs node est purement sécuritaire car cela permet
d'augmenter la robuster de notre cluster ( élément constituer de plusieurs nodes qui garde les données et permet l'indexation).
Avec un node(virtual or physical host ), si celui si devient down alors le cluster sera down aussi.

On utilise docker-compose pour la simplicité de configuration 

    ``` 
    docker kibana logs 
    ```
Avant d'utiliser kibana il faut attendre la fin de la configuration du serveur, on peut donc utiliser cette commande

## Formation du docker-compose
### elastic search service :
```
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
```
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

###  kibana service : 
pour kibana rien de plus simple la seule subtilité est de le mettre sur le même réseau que elastic et de le lié à l'host elastic
```
  kibana:
    image: docker.elastic.co/kibana/kibana:7.16.0
    container_name: kibana
    environment:
      ELASTICSEARCH_HOSTS: http://elasticsearch:9200
    ports:
      - 5601:5601
    networks:
      - es-network
    depends_on:
      - elasticsearch
```

###  filebeat service : 
```
  filebeat:
    image: "docker.elastic.co/beats/filebeat:7.16.0"
    container_name: filebeat
    user: root
    volumes:
      - ./config/filebeat.yml:/usr/share/filebeat/filebeat.yml:ro
      - /var/lib/docker:/var/lib/docker:ro
      - /var/run/docker.sock:/var/run/docker.sock
    networks:
      - es-network
```
ici on voit la présence de trois volumes :
  - le premier contient la configuration à partager avec le container filebeat ( composer localement d'un .yml)
  - le deuxième le lieu où se situe les logs de docker
  - le troisème le docker socket pour les logs qui ne seraient pas présent dans le deuxième volume

ce qui est important ici pour la configuration c'est de donner de donner les droits d'accès et d'appartement du fichier de configuration dans ./config/
comme suit : 
```
chown root filebeat.yml
chmod go-w /usr/share/filebeat/filebeat.yml
```

## Le fichier de configuration filebeat
```
filebeat.inputs:
- type: container
  paths: 
    - '/var/lib/docker/containers/*/*.log'

processors:
- add_docker_metadata:
    host: "unix:///var/run/docker.sock"

- decode_json_fields:
    fields: ["level","time","message"]
    target: "json"
    overwrite_keys: true

output.elasticsearch:
  hosts: ["elasticsearch:9200"]
  # hosts: ["http://localhost:9200"]
  indices:
    - index: "filebeat-%{[agent.version]}-%{+yyyy.MM.dd}"

logging.json: true
logging.metrics.enabled: true
```

- filebeat.inputs : correspond au lieux où sont situés les logs à l'intérieur du container
- add_docker_metadata : obtenir des informations supplémentaires sur les logs comme l'image utilisé, le nom du container ...
- decode_json_fields : pour parser les logs reçu de docker
- output.elasticsearch : cette partie sert à dire où sont importer les logs recceuillis par filebeat et comment les indicer






