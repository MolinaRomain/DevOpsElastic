### Réalisation : 
lancer la commande 
```
docker-compose up -d
```
pour instancier tout les containers
une fois réliser on vérifie que tout est bien initialisé du coté elastic search pour pouvoir lancer l'interface kibana

```
docker container logs kibana
```

ensuite sur la navigateur lancez :
http://localhost:5601/

une fois sur l'interface graphique de kibana
dans la barre de recherche il faut ajouter un index pattern
renseignez 
```
    filebeat-*
```
si tout est bien configuré, la detection de filebeat devrait être automatique, remplacez * par les logs que vous voulez, ou laisser comme ça pour tout récuperer
il faut configurer aussi le champ timestamp pour l'indexation des logs ( on a choisit pat default @timestamp)

Une fois tout cela finit il suffit juste d'aller dans le menu à gauche -> analytics -> discover 
pour voir la liste des logs collectés et leurs nombres

Il est aussi possible dans la partie Dashboard de configurer différents outils de statistiques pour avoir une meilleur vu d'ensemble des logs qui sont récuperer et pouvoir appliquer des tris sur certains champs des logs
