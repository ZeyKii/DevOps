# WIK-DPS-TP01

### Fonctionnement : 

Le programme renvoie, au format JSON, le headers de la requête quand on fait une requête HTTP GET sur le /ping de l'API. Le port est configurable grâce a la variable d'environnement `PING_LISTEN_PORT`. Tous les autres endpoints renvoient une erreur 404.

### Installation :

Clone le repo : 

Il faut ensuite créer la variable d'environement `PING_LISTEN_PORT` et la configurer.

### Configuration :

Pour changer le port, il faut changer la variable d'environnement `PING_LISTER_PORT`

### Lancement :

Lancer le projet grace a la commande ` node ./build/index `