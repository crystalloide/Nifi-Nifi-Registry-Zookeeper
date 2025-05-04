## Nifi et Nifi-Registry et Zookeeper :  
### lancement dans WSL2 ou VM Ubuntu avec docker compose

### Rappel : nous sommes ici : [https://github.com/crystalloide/Nifi-Nifi-Registry-Zookeeper](https://github.com/crystalloide/Nifi-Nifi-Registry-Zookeeper)

    cd ~

    rm -Rf Nifi-Nifi-Registry-Zookeeper
    
    git clone https://github.com/crystalloide/Nifi-Nifi-Registry-Zookeeper
    
    cd ~/Nifi-Nifi-Registry-Zookeeper

#### On rajoute les futurs conteneurs dans le host de la machine : 
    sudo vi /etc/hosts

#### rajouter 
    192.168.0.21 nifi1
    192.168.0.22 nifi2
    192.168.0.23 nifi3
    192.168.0.30 nifi-registry




#### Nettoyage des volumes restants d'éventuels lancements précédents : 
    docker volume prune -a -f
    docker volume ls
    docker ps -a


#### Nettoyage de répertoire restant d'un éventuel lancement précédent : 
    sudo rm -Rf /home/user/ssl/nifi_certs
    sudo rm -Rf /home/user/ssl
    mkdir -p /home/user/ssl/nifi_certs
    sudo chmod 777 -Rf /home/user/ssl/nifi_certs
    ls


#### On affiche le contenu de notre fichier qui servira à docker compose : (version Apache Nifi 1.28.1 )
    cd ~/Nifi-Nifi-Registry-Zookeeper
    cat 'TP06c_-_Cluster_Nifi_1.28.1_sécurisé_avec_Zookeeper_Nifi_registry_et_Nifi_Toolkit_dans_docker_compose.yml'
    
#### On affiche le contenu de notre fichier qui servira à docker compose : (version Apache Nifi 2.3.0 )
    cd ~/Nifi-Nifi-Registry-Zookeeper
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


## Lançons donc l'environnement : (version Apache Nifi 1.28.1 )
______________________________________________________________	
    docker compose -f 'TP06c_-_Cluster_Nifi_1.28.1_sécurisé_avec_Zookeeper_Nifi_registry_et_Nifi_Toolkit_dans_docker_compose.yml' up -d

______________________________________________________________	
## Autre possibilité : Lançons donc l'environnement : (version Apache Nifi 2.3.0 )
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

______________________________________________________________
####  Ce fichier docker-compose.yml configure NiFi avec les éléments suivants :
####  - image officielle Apache NiFi la plus récente
####  - le port HTTPS 8443 du conteneur est mappé au port 8443 de l'hôte, ce qui permettra d'accéder à NiFi via https://localhost:8443/nifi1
####  - Des variables d'environnement sont définies pour configurer le port HTTPS et créer un utilisateur unique pour l'authentification.
####  - Des volumes sont montés pour persister les données et la configuration de NiFi.
####  - Le conteneur est configuré pour redémarrer automatiquement en cas d'arrêt inattendu.
#### 
####  - NIFI_SECURITY_USER_AUTHORIZER=single-user-authorizer permet d'activer l'autorisation à utilisateur unique
####  - NIFI_SECURITY_USER_LOGIN_IDENTITY_PROVIDER=single-user-provider permet de configurer le fournisseur d'identité sur "utilisateur unique"
####    Ces ajouts, combinés avec les variables SINGLE_USER_CREDENTIALS_USERNAME et SINGLE_USER_CREDENTIALS_PASSWORD, 
####    permettent de configurer l'authentification à utilisateur unique.
#### 
####  - NIFI_WEB_HTTPS_HOST=0.0.0.0
####  - NIFI_WEB_HTTPS_PORT=8443
####    La variable NIFI_WEB_HTTPS_HOST=0.0.0.0 permet à NiFi d'écouter sur toutes les interfaces réseau du conteneur.
####    La variable NIFI_WEB_HTTPS_PORT=8443 spécifie le port HTTPS sur lequel NiFi écoutera.
#### 
####  	 Zookeeper : 
####  - gestion externe à Nifi via le paramètre : NIFI_STATE_MANAGEMENT_EMBEDDED_ZOOKEEPER_START: "false"
______________________________________________________________

______________________________________________________________
####  Aspects sécurité : il faudrait : 
####  - Modifier les mots de passe par défaut (password123)
####  - Régénérer la clé NIFI_SENSITIVE_PROPS_KEY
####  - Ajouter une authentification utilisateur supplémentaire
______________________________________________________________

______________________________________________________________
#### Pour se se connecter sur un conteneur : ici nifi1 : 
    docker exec -it nifi1 bash 

    cat /opt/nifi/nifi-current/conf/*.xml | grep "Connect String"

    exit



###### On vérifie que l'on peut désormais accéder à l'interface web de NiFi en ouvrant https://nifi1:8443/nifi 
###### Note : il faudra accepter le certificat auto-signé généré par NiFi lors de la première connexion.

    netstat -anl | grep 844

#### Affichage : 
	tcp        0      0 0.0.0.0:8445            0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:8444            0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:8443            0.0.0.0:*               LISTEN     
	tcp6       0      0 :::8445                 :::*                    LISTEN     
	tcp6       0      0 :::8444                 :::*                    LISTEN     
	tcp6       0      0 :::8443                 :::*                    LISTEN     



#### Et à partir du navigateur (Firefox ici), 
#### on utilise les identifiants de connexion sur l'UI Nifi :
URL : https://nifi1:8443/nifi

#### Accepter le certificat auto-signé et le risque indiqué : 
#### Dans notre exemple, les valeurs de connexion sont celles fixées dans le docker-compose.yml : 

      - SINGLE_USER_CREDENTIALS_USERNAME=nifi
      - SINGLE_USER_CREDENTIALS_PASSWORD=nifipassword

#### Saisir donc le login : 
    nifi
    
#### Puis le mot de passe : 
    nifipassword

#### Remarque  : pour arrêter si besoin : (version Apache Nifi 1.28.1 )
    docker compose -f 'TP06c_-_Cluster_Nifi_1.28.1_sécurisé_avec_Zookeeper_Nifi_registry_et_Nifi_Toolkit_dans_docker_compose.yml' down
    
#### Remarque  : pour arrêter si besoin : (version Apache Nifi 2.3.0 )
    docker compose -f 'TP06d_-_Cluster_Nifi_2.3.0_sécurisé_avec_Zookeeper_Nifi_registry_et_Nifi_Toolkit_dans_docker_compose.yml' down
    
### Fin du TP
