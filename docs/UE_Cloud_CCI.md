# UE Introduction au Cloud Computing

## TP #1 d’introduction au Cloud Computing

> **Objectif :** Cette première séance de TP  a pour objectif de vous
> familiariser avec l’interface Horizon qui permet d’exploiter simplement une
> plateforme Cloud Openstack (financée par le département informatique et [la
> région](http://www.auvergnerhonealpes.eu/]).  Nous utiliserons pour cette
> séance une image système « clé en main »,  que vous pourrez instancier et sur
> laquelle vous devrez vous connecter via SSH.


### PARTIE 1 : Prise en main (Horizon / SSH / Service)


**Étape préliminaire :** 

Connectez-vous à la plateforme d’administration Horizon de Openstack via votre
navigateur en saisissant dans la barre de navigation l’URL suivante :

  * [http://cloud-info.univ-lyon1.fr/horizon](http://cloud-info.univ-lyon1.fr/horizon)
  * Domaine : **univ-lyon1.fr**
  * login/password de votre compte étudiant Lyon 1.


Pour vous connecter à votre instance de machine virtuelle vous aurez besoin
d’un jeu de clé SSH (clés publique/privé). Deux solutions s’offre à vous : 

  1. Générer un jeu de clé via l’outil `ssh-keygen` que vous importerez
    (selectionnez l’onglet « Access and security -> Key Pair -> Import Key
Pair » puis copier/coller la clé publique (.ssh/id_rsa.pub))  ou, 
  1. Générer une clef lors de la création de votre première VM. Pour cela
     l'onglet de gestion de clef lors de la création de la VM est assez simple
d'utilisation. **Attention**, il faut bien télécharger la clef privée lors de
la création car c'est la seul fois où vous y aurez accès. De plus, vous devez
récupérer la clef publique (en cliquant sur la petite clef a coté de
l'empreinte de la clef créée. Ces deux clefs doivent être conservées pour plus
tard.

	
**Étape #1 :** 

Dans le cadre de ce TP nous vous proposons de sélectionner une
« petite » image système pré-configurée. Pour cela, allé sur l’onglet
« instances » puis « Launch Instance ». Nous vous recommandons (sous réserve de
disponibilité) celle nommée *Ubuntu 18.04 LTS Bionic Beaver*. Sélectionnez la source de
l’image (menu déroulant Instant boot source -> Boot from Image).  Sélectionné
ensuite un type (aussi appelé flavor) de machine virtuelle qui sera adaptée à
l’image sélectionnée précédemment et à l’usage  que vous en ferez. Toujours
dans le contexte de ce TP, un flavor *m1.xsmall* proposant 10GB d’espace
disque, 1 GB de RAM et 1 vCPU conviendront largement.

Finalement, avant de créer l’instance et de la lancer (Launch instance),
assurez-vous que votre clé publique à bien été sélectionnée (Remarque : Cette
clé sera copiée dans la VM au moment de sa création. Cela vous permettra de
vous connecter à votre VM sans avoir à saisir de mot de passe).  Nommez
l’instance et lancer là, puis patientez…

Lorsque celle-ci sera opérationnelle, notez l’adresse IP fixe qui lui a été
associée (cf. interface Horizon) et connectez-vous via SSH en spécifiant le nom
de votre clé publique à l’aide de l’option *-i* de SSH le cas échéant.

Exemple : 
`$ ssh -i ./chemin/vers/maCleSsh ubuntu@adresseIPdeVotreVM`

*Remarque 1 :* Le nom de votre clé SSH est peut être *id_rsa* ou *id_dsa*, ou
*nomcle.pem*.

*Remarque 2 :* Le nom d’utilisateur par défaut est *ubuntu* pour cette image
système mais sera très probablement différent sur une image issue d’une
distribution autre que Ubuntu.

Toutes  les VM sont, dans le cadre de ce TP, situées dans le même sous réseau.
Vérifiez en « pingant » la VM d’un de vos camarades (vous aurez besoin de son
adresse IP).

> Vous avez réussi à vous connectez à votre VM ? Pinger une autre VM à partir
> de votre VM ? Félicitation ! Passez à l’étape suivante.


**Étape #2 :**

Usage de la VM : Nous allons à présent utiliser la VM. Vous remarquerez par
vous même qu’il n’y a pas différences avec l’administration  d’une machine
physique classique. Pour nous en convaincre nous allons vous demander d’y
installer Apache2.

```sh
$ sudo apt-get update
$ sudo apt-get install apache2
```

Une fois installé, ouvrir un nouvel onglet  de votre navigateur et connectez
vous à votre serveur web Apache fraichement installé.

`http://adresseIPdeVotreVM/`
(exemple : http://192.168.74.136)

A présent modifiez le contenu du fichier `/var/www/html/index.html`,
sauvegardez et vérifiez avec votre navigateur que la modification faite
apparait bien.

> Ça fonctione ? Bravo !


**Étape #3 : Snapshot (optionnelle)**

 - Snapshot : Toujours à l’aide de l’interface d’administration Horizon, réalisez
un snapshot de votre instance (bouton « Create Snapshot » ou « Créez un
instantané »). Patientez…

 - Démarrez une seconde VM à partir du snapshot réalisé dans l’étape précédente
(Boot from snapshot, Launch Instance, cf. Étape #1). Vous observerez que Apache
fonctionne desormais sur la nouvelle machine.

 



### PARTIE 2 : Personnalisation d’une VM 


**Objectif :** 

Installez une application PHP appelée *Tiny* disponible sur
GitHub.  (*Tiny is an extremely simple naked demo PHP application*). Récupérez
le projet Tiny :
[https://github.com/panique/tiny](https://github.com/panique/tiny) 
Avant d’installer et configurer Tiny vous devrez au par avant installer
Apache2, PHP et MySQL (cf.  Annexes). Puis copiez et désarchivez
tiny-master.zip dans `/var/www/html`.  Suivre le guide d’installation
(README.md). Validez le bon fonctionnement avec votre navigateur avec l’url
suivante : http://*adresseIPvm*/tiny-master
 

**Étape Finale :** 

 - **DEMO** : Faite une démo à votre enseignant.
 - **TOMUSS** : Déposez l'adresse IP de votre VM dans Tomuss (UE Introduction au Cloud Computing). 

### ANNEXES :

Aide pour l’installation de Apache2 , PHP et MySQL.

Pour vous inspirer : [http://www.dev-metal.com/installsetup-basic-lamp-stack-linux-apache-mysql-php-ubuntu-14-04-lts/](http://www.dev-metal.com/installsetup-basic-lamp-stack-linux-apache-mysql-php-ubuntu-14-04-lts/)

```sh
sudo apt-get update 
sudo apt-get install apache2
sudo apt-get install php
sudo apt-get install mysql-server 
sudo apt-get install php-mysql 
sudo service apache2 restart
```

Récupérer le projet Tiny sur
[https://github.com/panique/tiny](https://github.com/panique/tiny) (*Astuce:*
`wget` du fichier .zip)

Désarchiver le zip dans `/var/www/html`

Construisez et alimentez la BdD avec les scripts fourni.
```sh
mysql -h localhost -u root -p < _installation/01-create...
mysql -h localhost -u root –p < _installation/02-create...
mysql -h localhost -u root –p < _installation/03-insert...

http://localhost/tiny-master
```

**Pour que ça fonctionne avec une version récente de MySQL** :

Modifier `application/config/config.php`

```php
define('DB_TYPE', 'mysql');
define('DB_HOST', '127.0.0.1');
define('DB_NAME', 'tiny');
define('DB_USER', 'tiny');
define('DB_PASS', 'tinypasswd');
```
Créer un nouvel utilisateur dans la base de données :

```
mysql --user=root mysql

CREATE USER 'tiny'@'localhost' IDENTIFIED BY 'tinypasswd';
GRANT ALL PRIVILEGES ON *.* TO 'tiny'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;

``` 

*Sources de la solution :* [askubuntu.com](https://askubuntu.com/questions/763336/cannot-enter-phpmyadmin-as-root-mysql-5-7/763359#763359)

---

## TP #2 Introduction aux architectures Web Orientées Services 

> **Objectifs :** L’objectif de ce TP est de proposer une introduction aux
> architectures orientés Services (Service Oriented Architecture (SOA)), très
> populaire pour les services web et cloud, et à l’architecture Model Vue
> Contrôleur (MVC), très populaire dans l’industrie. Pour cela nous vous
> proposons de développer deux services web, l’un donnant l’heure et la date du
> jour, et l’autre, récupérant cette date et cette heure pour en générer une page
> web.  Ces services web seront développés en Python un langage de programmation
> orienté objet. Ce TP servira donc d’introduction à Python un langage
> particulièrement populaire. Il est conçu prioritairement pour la lisibilité du
> code. 

Le sujet est disponible à l'[adresse suivante](https://perso.univ-lyon1.fr/jean-patrick.gelas/doc/cci/TPsoa2019.pdf).

---

## TP #3 Projet "Libre"

> **Objectif :** Concevoir et développer (en binôme) une application qui
> servira  une page web sur laquelle seront mis en forme des données provenant
> d'un service accessible via une API.  

Il existe une multitude de services disponibles sur Internet qui proposent
des données en tout genre. Cet article liste par exemple [18 API
amusantes](https://medium.com/@vicbergquist/18-fun-apis-for-your-next-project-8008841c7be9).

Pour préparer ce sujet et l'illustrer nous avons écrit une petite
application qui exploite les données du service [swapi](https://swapi.co/)
(l'API Star Wars). Notre application est disponible à l'adresse suivante
[http://192.168.239.77:8080](http://192.168.239.77:8080). En passant le nom d'un des personnages de
StarWars dans l'URL on affiche de manière élégante ses caractéristiques (et le
cas échéant une photo même). Par exemple, [http://192.168.239.77:8080/luke](http://192.168.239.77:8080/luke),
pour obtenir des informations sur *Luke Skywalker*.

Votre objectif est donc de concevoir une application sur le même principe. Par
exemple, en passant le nom d'une ville dans l'URL on obtiendrait la température
qu'il y fait ; le nom d'un pays on obtient le nombre d'habitant ; le numéro
d'une station Vélo'V on obtient le nombre de vélo restant ; etc...  

> Prenez le temps de sélectionner l'API avec laquelle vous allez jouer.

Le choix de la solution technique que vous allez utiliser est de votre ressort.
Vous pouvez reprendre par exemple le TP#2 et la librairie *web.py* (simple à
utiliser mais un peu obsolète) ou la librairie *flask* plus moderne.
Pour information nous nous avons fait le choix d'utiliser le langage Javascript
interprété avec le moteur *node.js*, en nous appuyant entre autre sur la
librairie *express* (nous avons fait ce choix car nous n'avions jamais
programmé en Javascript... C'était l'occasion de faire connaissance).
 
**Évaluation** : Ce projet sera évalué. La date limite de rendu n'est pas encore fixée.
Vous devrez fournir dans Tomuss : 

  - L'adresse IP de votre VM sur laquelle fonctionnera votre service sur le
    **port 8080**.
  - Un fichier PDF contenant :
    1. Vos nom et prénom  (binôme)
    2. Un titre
    3. Un *snapshot* de l'écran + l'URL permettant d'obtenir cet écran.
    3. Un paragraphe expliquant brièvement quels informations présente votre page web.
    4. Une liste qui énumère les solutions techniques (ex: Javascript/node.js,
       librairies *express*, *request* et *ejs*).
    5. Le code source du ou des fichiers que vous avez rédigé (Remarque: utilisez une petite police de
       caractère pour les codes source).


![Screenshot](img/snap-starwars-api.png) [http://192.168.239.77:8080/leia](http://192.168.239.77:8080/leia) 💕







