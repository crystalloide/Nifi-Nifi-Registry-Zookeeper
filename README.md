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



__________________________________________________________________________________________________
## Autre expérimentation : 
__________________________________________________________________________________________________
## Pour lancer un ensemble Nifi et Kafka :

    cd ~/Nifi-Nifi-Registry-Zookeeper
    docker compose -f 'TP05a_-_Lancement_Kafka_docker_Kafka-NiFi.yml' up -d


__________________________________________________________________________________________________
## On vérifie que les conteneurs sont bien en cours d'exécution : 
docker ps -a

## Affichage : 
	CONTAINER ID   IMAGE                              COMMAND                  CREATED              STATUS              PORTS                                                                                                                                        NAMES
	c074d040ec51   confluentinc/cp-kafka:latest       "/etc/confluent/dock…"   About a minute ago   Up About a minute   0.0.0.0:9092->9092/tcp, :::9092->9092/tcp                                                                                                    kafka
	bc8c339e3498   confluentinc/cp-zookeeper:latest   "/etc/confluent/dock…"   About a minute ago   Up About a minute   2181/tcp, 2888/tcp, 3888/tcp                                                                                                                 zookeeper
	5edd2bf720dd   apache/nifi:latest                 "../scripts/start.sh"    About a minute ago   Up About a minute   0.0.0.0:8000->8000/tcp, :::8000->8000/tcp, 0.0.0.0:8080->8080/tcp, :::8080->8080/tcp, 0.0.0.0:8443->8443/tcp, :::8443->8443/tcp, 10000/tcp   nifi


## On vérifie que le conteneur Kafka est bien à l'écoute : 
netstat -anl | grep 909

## Affichage pour Kafka : 
	tcp        0      0 0.0.0.0:9092            0.0.0.0:*               LISTEN     
	tcp6       0      0 :::9092                 :::*                    LISTEN     



__________________________________________________________________________________________________
#### Connection à l'IUI nifi :  
__________________________________________________________________________________________________

#### On se connecte sur le conteneur Nifi pour récupérer les identifiants de connexion (générés aléatoirement dans le cas présent) : 
#### Remarque : on aurait pu aussi les passer en paramètres dans le fichier docker-compose pour indiquer un user/mot de passe.
    docker exec -it nifi bash 

    cat /opt/nifi/nifi-current/logs/*.log | grep "Generated"

#### Affichage : (exemple) 
#### nifi@82c0f0bf857f:/opt/nifi/nifi-current$ cat /opt/nifi/nifi-current/logs/*.log | grep "Generated"
#### Generated Username [150248a6-bf21-455a-ba2e-68b28cf4a5b8]
#### Generated Password [fdnaz55nAokJ6wOjh71KD4OHYLGd61LH]
#### 2025-03-13 10:11:19,047 INFO [main] o.a.n.b.c.GetRunCommandBootstrapCommand Generated Self-Signed Certificate Expiration: 2025-05-12
#### 2025-03-13 10:11:19,047 INFO [main] o.a.n.b.c.GetRunCommandBootstrapCommand Generated Self-Signed Certificate SHA-256: C9BFB568B4D36760DB463650D0B74C86DAE4A31F0638776CF2760599AE1EFA4E


#### Les valeurs qui nous intéressent (dans notre exemple : 
	Generated Username [150248a6-bf21-455a-ba2e-68b28cf4a5b8]
	Generated Password [fdnaz55nAokJ6wOjh71KD4OHYLGd61LH]

#### On ressort du conteneur "nifi" : 
    exit


#### Et à partir du navigateur (Firefox ici), 
#### on utilise les identifiants de connexion sur l'UI Nifi :
### URL : 
    https://localhost:8443/nifi


__________________________________________________________________________________________________
#### Utilisation de Kafka :  
__________________________________________________________________________________________________

#### On vérifie que le conteneur Kafka est bien à l'écoute : 

netstat -anl | grep 909

#### Affichage pour Kafka : 
	tcp        0      0 0.0.0.0:9092            0.0.0.0:*               LISTEN     
	tcp6       0      0 :::9092                 :::*                    LISTEN     



#### On va dans le conteneur pour initaliser notre environnement kafka 
#### et pouvoir s'en servir ensuite dans nifi : 

    docker exec -it kafka bash 


#### On regarde la version de kafka :
    kafka-topics -version



#### Affichage :
    7.8.2-ccs

#### Pour faire l'équivalence entre les version Confluent et Open Source :
#### https://www.kineticedge.io/resources/confluent-community-versions/



#### On crée le topic "test-topic" :
    kafka-topics --create --topic test-topic  --bootstrap-server localhost:9092 

#### Affichage :  
	Created topic test-topic.


#### On alimente le topic "test-topic" avec quelques messages :
    kafka-console-producer --topic test-topic --bootstrap-server localhost:9092

#### Saisir quelques messages : 
    Test1
    Test2
    ...
#### (Ctrl-C pour quitter)


#### On vérifie que le topic "test-topic" contient bien nos quelques messages :
kafka-console-consumer --topic test-topic --bootstrap-server localhost:9092 --from-beginning

#### Affichage : 
Test1
Test2
...
#### (Ctrl-C pour quitter)



__________________________________________________________________________________________________
#### Intéraction entre Nifi et Kafka :  
__________________________________________________________________________________________________

#### On retourne maintenant dans le canevas nifi pour définir et paramétrer un processeur :
#### Dans le navigateur web : URL : https://localhost:8443/nifi


#### Pour intéragir avec Kafka depuis Nifi  :  

_________________________________________________
#### Ecriture de messages dans un topic Kafka :
_________________________________________________
		
#### On ajoute d'abord un processor GenerateFlowFile pour générer du JSON que l'on va ensuite écrire dans notre topic
		
#### Remarque : on écrit impérativement du JSON ici (du simple texte ne sera pas pris en compte)

#### Exemple : 

[{"user_id":1,"username":"ecartner0","email":"emackim0@lulu.com","age":40,"gender":"Agender","country":"China","job_title":"VP Sales","salary":62400.29,"join_date":"7/21/2012","active_status":false},
{"user_id":2,"username":"jcelle1","email":"twallerbridge1@webmd.com","age":27,"gender":"Male","country":"Brazil","job_title":"Food Chemist","salary":104092.27,"join_date":"8/29/2018","active_status":true}]



#### On ajoute ensuite le processor PublishKafka sur le canevas de l'UI Nifi : 
#### (sera "PublishKafka 2.3.0" à la date d'actualisation de ce TP)


#### Paramètrage du processor sur Kafka : (onglet Properties dans la fenêtre "Edit Processor")
#### Ici par exemple PublishKafka_2_3_0

	Kafka Connection Service		No value set					=> 	Faire "+" (= "Add  Controller Service")
																		puis choisir "Kafka3ConnectionService"
																		et enfin activer le controller "Kafka3ConnectionService"
>>>	Topic Name						test-topic
	Failure Strategy				Route to Failure
	Delivery Guarantee				Guarantee Replicated Delivery
	Compression Type				none
	Max Request Size				1 MB
>>>	Transactions Enabled			false
	Transactional ID Prefix			No value set
	Partitioner Class				DefaultPartitioner
>>	Partition						0							<<<< IMPORTANT : on compte à partir de 0 dans Kafka :-)
	Message Demarcator				No value set
	Record Reader					No value set
	Record Writer					No value set
	Kafka Key						No value set




#### Dans l'onglet "Relationships" du processor PublishKafka 2.3.0 : 
#### sélectionner ("x" ci-dessous ) et faire "Apply" 

Automatically Terminate/Retry Relationships

failure
	x terminate		_ retry

	Any FlowFile that cannot be sent to Kafka will be routed to this Relationship


success

	x terminate		_ retry
	
	FlowFiles for which all content was sent to Kafka.
 




#### Paramétrage du Controller Service Kafka : 
#### =>Faire "Edit Controller Service" sur "Kafka3ConnectionService 2.3.0"


>>>	Bootstrap Servers				kafka:29092
	Security Protocol				PLAINTEXT
>>>	SASL Mechanism					PLAIN
>>>	SASL Username					admin
>>>	SASL Password					admin
	Kerberos User Service			No value set
	Kerberos Service Name			No value set
	SSL Context Service				No value set
	Transaction Isolation Level		Read Committed
	Max Poll Records				10000
	Client Timeout					60 sec
	Max Metadata Wait Time			5 sec
	Acknowledgment Wait Time		5 sec 


#### Important: Veiller à faire "enabled " sur le service (et seulement le service)



#### On teste enfin la validité du processeur ainsi paramétré :  
#### Verification Results : ok 

#### Affichage : 
	Verification Results
		v 	Perform Validation
			Component Validation passed
		
		v 	Determine Topic Partitions
			Determined that there are 1 partitions for topic test-topic
			
			
	

#### Exemple pour une version Nifi plus ancienne (1.28.x par exemple) :

#### Paramètrage du processor sur Kafka : (onglet Properties dans la fenêtre "Edit Processor")
#### Ici par exemple PublishKafka_2_6_1.27.0

>	Kafka Brokers					kafka:29092
> 	Topic Name						test-topic
> 	Use Transactions				false
	Message Demarcator				No value set
>	Failure Strategy				Route to Failure
>	Delivery Guarantee				Guarantee Single Node Delivery
	Attributes to Send as Headers (Regex)		No value set
	Message Header Encoding			UTF-8
>	Security Protocol				PLAINTEXT
>	SASL Mechanism					PLAIN	
	Kerberos Credentials Service	No value set
	Kerberos User Service			No value set
	Kerberos Service Name			No value set
	Kerberos Principal				No value set
	Kerberos Keytab					No value set
>	Username						admin
>	Password						admin
	SSL Context Service				No value set
	Kafka Key						No value set	
>	Key Attribute Encoding			UTF-8 Encoded
>	Max Request Size				1 MB
>	Acknowledgment Wait Time		5 secs
>	Max Metadata Wait Time			5 sec
	Partitioner class				DefaultPartitioner
>	Partition						0							<<<< IMPORTANT : on compte à partir de 0 dans Kafka :-)
>	Compression Type				none





_________________________________________________
#### Ecriture de messages dans un topic Kafka :
_________________________________________________
				
#### On fait de même avec cette fois un processeur qui va écrire les messages précédemment lus : 

### On drop le processor PublishKafka sur le canevas de l'UI Nifi : 
#### (ca sera "ConsumeKafka 2.3.0" à la date d'actualisation de ce TP)



>>>	Kafka Connection Service		Kafka3ConnectionService  ( => Ne pas en créer un nouveau : il faut juste le sélectionner dans la liste)
>>> Group ID						CGRP1
	Topic Format					names
>>>	Topics							test-topic
	Auto Offset Reset				latest
	Commit Offsets					true
	Max Uncommitted Time			1 sec
	Header Name Pattern				No value set
	Header Encoding					UTF-8
	Processing Strategy				FLOW_FILE 



#### Dans l'onglet "Relationships" du processor ConsumeKafka 2.3.0 : 
#### sélectionner ("x" ci-dessous ) et faire "Apply" 

Automatically Terminate/Retry Relationships


success
																		Number of Retry Attemps 
	x terminate		x retry												10
	
	FlowFiles for which all content was sent to Kafka.					Retry Back Off Policy 
																		10 mins




#### Remarque : exemple pour cette fois une version de Nifi plus ancienne (1.28x) : 

#### ConsumeKafka_2_6 1.27.0 


>	Kafka Brokers			kafka:29092
>	Topic Name(s)			test-topic
>	Topic Name Format		names
>	Group ID				CGRP1
	Commit Offsets			true
	Max Uncommitted Time	1 secs
>	Honor Transactions		false
	Message Demarcator		No value set
	Separate By Key			false
>	Security Protocol		PLAINTEXT
>	SASL Mechanism			PLAIN
	Kerberos Credentials Service	No value set
	Kerberos User Service			No value set
	Kerberos Service Name			No value set
	Kerberos Principal				No value set
	Kerberos Keytab					No value set
>	Username				admin
>	Password				admin
	SSL Context Service		No value set
>	Key Attribute Encoding	UTF-8 Encoded
>	Offset Reset			latest
	Message Header Encoding		UTF-8
	Headers to Add as Attributes (Regex)		No value set
	Max Poll Records		10000
>	Communications Timeout	60 secs


_________________________________________________
#### Pour voir dans la log les informations confirmant que l'on a bien lu et écrit les messages depuis kafka : 
    docker exec -it nifi bash
    ls /home/nifi 
    exit

_________________________________________________
#### Remarque  : pour stopper si besoin :
    docker compose -f 'TP05a_-_Lancement_Kafka_docker_Kafka-NiFi.yml' stop

_________________________________________________
#### Remarque  : pour supprimer complètement si besoin :
    docker compose -f 'TP05a_-_Lancement_Kafka_docker_Kafka-NiFi.yml' down


_________________________________________________    
### Fin du TP
_________________________________________________
