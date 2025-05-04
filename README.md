## Nifi et Nifi-Registry et Zookeeper :  lancement dans WSL2 ou VM Ubuntu avec docker compose

### Rappel : nous sommes ici : [https://github.com/crystalloide/mongoDB](https://github.com/crystalloide/Nifi-Nifi-Registry-Zookeeper/edit/main/README.md)

    cd ~

#### On rajoute les futurs conteneurs dans le host de la machine : 
    sudo vi /etc/hosts

#### rajouter 
    192.168.0.21 nifi1
    192.168.0.22 nifi2
    192.168.0.23 nifi3
    192.168.0.30 nifi-registry

#### On affiche le contenu de notre fichier qui servira à docker compose :
    cat 'TP06d_-_Cluster_Nifi_2.3.0_sécurisé_avec_Zookeeper_Nifi_registry_et_Nifi_Toolkit_dans_docker_compose.yml'

______________________________________________________________
#### Configuration :
______________________________________________________________	
####
#### Cluster Zookeeper :
#### - 3 nœuds avec configuration automatique
#### 
#### Persistence des données via volumes Docker
#### 
#### Communication interne sur les ports 2181/2888/3888
#### 
#### Sécurité TLS :
#### - Génération automatique des certificats avec NiFi Toolkit
#### - Certificats partagés entre les nœuds via volume Docker
#### - Configuration HTTPS automatique pour chaque nœud
#### 
#### NiFi Registry :
#### - Instance séparée sur le port 18080
#### - Intégration automatique avec le cluster
#### 
#### Zookeeper : 
#### - gestion externe à Nifi via le paramètre : NIFI_STATE_MANAGEMENT_EMBEDDED_ZOOKEEPER_START: "false"
#### 


______________________________________________________________	
## Lançons donc l'environnement :
______________________________________________________________	
    docker compose -f 'TP06d_-_Cluster_Nifi_2.3.0_sécurisé_avec_Zookeeper_Nifi_registry_et_Nifi_Toolkit_dans_docker_compose.yml' up -d

______________________________________________________________	
## Accès :
______________________________________________________________	
####  - 3 noeuds NiFi :  
#### 					login : 		nifi
#### 					mot de passe : 	nifipassword
#### 
####  					https://nifi1:8443/nifi/
####  					https://nifi2:8443/nifi/
#### 					https://nifi3:8443/nifi/
#### 
#### 
####  - NiFi Registry : http://localhost:18080  / http://nifi-registry:18080/nifi-registry
#### 
______________________________________________________________	

## Fin du TP
