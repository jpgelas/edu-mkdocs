# TP Attaques bruyantes ou scan réseau

> Responsables : Jp Gelas, Thomas Begin

- - - 

![Nmap](img/nmap.png "Nmap")

Ce premier sujet de TP propose une introduction à `Nmap`, un outil de *scanning* relativement efficace pour un administrateur mais néanmoins beaucoup trop "bavard" pour être utilisé sans connaissances préalable par un pentesteur.



### Qu'est ce que Nmap ?

Extrait du site web officiel [https://nmap.org/](https://nmap.org/)


> Nmap ("Network Mapper") is a free and open source (license) utility for network discovery
> and security auditing.  Many systems and network administrators also find it useful for 
> tasks such as network inventory, managing service upgrade schedules, and monitoring host or 
> service uptime.

Nmap est souvent utilisé pour déterminer les hôtes actifs dans un réseau, les
ports ouverts sur ces hôtes, les services fonctionnant sur ces ports ouverts et
l'identification de la version de ce service sur ce port.

### Comment fonctionne Nmap ?

Nmap fonctionne en envoyant de nombreux paquets vers la cible, puis la cible
répond d'une manière spécifique. Par exemple, Nmap peut envoyer un paquet à la
cible, explicitement sur le port 80 (qui est généralement un service HTTP). La
cible répond alors de manière spécifique, ce qui correspond à un état (*open,
closed, filtered*). Si le port est ouvert, Nmap identifie en fonction de la
réponse quel type de service HTTP est en cours d'exécution (Apache, Nginx, IIS,
etc.). Ensuite, une autre vérification est utilisée pour identifier la version,
et ainsi de suite.

Nmap a un enregistrement des services qui correspond à un type de réponse à ce
type de requête, ce qui conduit éventuellement à ce que Nmap détermine
correctement quel est le service et sa version. Cela n'est cependant pas
infaillible, car Nmap confond parfois un service fonctionnant sur ce port (si
vous ne faites pas une vérification beaucoup plus fastidieuse en utilisant les
bonnes options) parce que ce port est couramment utilisé pour un service
spécifique. Notez que les scans de Nmap sont mieux exécutés avec les privilèges
d'un administrateur ou de root car Nmap envoie des paquets bruts (*raw*) et il
est nécessaire d'être un utilisateur privilégié pour envoyer ces paquets bruts.

### Usage de Nmap

```
nmap [scan type] [options] [target specification]
```

#### Types de scan Nmap courants

Nmap propose différents types de scan en fonction de vos besoins. Les plus courants sont le scan SYN (-sS), le scan TCP connect (-sT) et le scan UDP (-sU). Notez que le scan SYN et le  scan TCP utilisent le *three-way handshake* :

  * Le scan SYN (-sS) identifie un port qui doit être ouvert en envoyant un "SYN" à la cible. Si elle reçoit un SYN-ACK ou un SYN, elle marque ce port comme étant ouvert. S'il reçoit un "RST", il marque le port comme étant filtré (filtered), c'est à dire inaccessible à cause d'un pare-feu par exemple.
  * Le scan TCP (-sT) identifie un port comme étant ouvert en attendant la fin du *three-way handshake*.
  * Le scan UDP (-sU) est utile pour identifier les ports UDP ouverts sur une cible. Il envoie des paquets UDP spécifiques à des ports UDP connus.

#### Les options courantes

Nmap propose des centaines d'options. Voici les plus utilisées. Pour plus d'informations n'hésitez pas à consulter la documentation.

  * Sauvegarde : Sauvegarder vos scan dans un fichier utilisez l'option -oN
  * Verbosity : Pour rendre Nmap plus bavard à l'écran -v, -vv, -vvv
  * Timing : Vous pouvez utiliser des *timing template* en utilisant l'un des flags -T0,-T1,-T2,-T3,-T4,-T5 qui correspondent respectivement à paranoïaque, sournois, poli, normal, agressif et fou (paranoid, sneaky, polite, normal, aggressive, and insane). Ces templates déterminent le degré d'agressivité de votre analyse et sont utiles en fonction du débit et des ressources du réseau sur lequel vous vous trouvez. Les scans -T0 et -T1 sont utiles pour éviter les IDS (Intrusion Detection System), mais peuvent être trop lents. T3 ou normal est le comportement par défaut de Nmap lorsqu'aucun de ces flags n'est mentionné.

#### Spécification de la cible

Vous pouvez spécifier le ou les port(s) en utilisant l'option `-p`

```
# Specifying a single port(port 21):
nmap -p21 10.10.10.10
# Specifying a port range(port 21 to 1000):
nmap -p21-1000 10.10.10.10
# Specifying a port range but exclude port 22:
nmap -p21-1000 -exclude-ports 22 10.10.10.10 
```

Nmap accepte des nom d'hôtes, des adresses IP ou un *range* d'adresses.

```
# Scanning a specific hostname:
nmap target.domain.com 
# Scanning a specific IP address:
nmap 10.10.10.10
# Scanning a network range:
nmap 10.10.10.1-254 or nmap 10.10.10.0/24
# Scanning a network range but exclude few IP addresses:
nmap 10.10.10.0/24 --exclude 10.10.10.167
```

**Remarque** : Nmap scanne les 1000 premiers ports TCP les plus courants et les plus utilisés si vous ne spécifiez pas de port spécifique.

### A vous de jouer ?

  * Scannez un hôte cible de votre choix pour lequel vous avez obtenu une autorisation.  
  * Sauvegardez le résultat du scan dans un fichier.  
  * Déterminez quel sont les ports ouverts, fermés ou filtrés.  
  * Pour chaque port ouvert, déterminez quel service est disponible et quel version du service est en cours d'exécution.  
  * Ajoutez une option pour exclure le port 22.  
  * N'ignorez pas UDP. Il se peut qu'un service malveillant (backdoor) tourne sur l'hôte que vous êtes en train d'auditer.  
  * Enfin observez avec Wireshark le trafic généré par Nmap et jouez avec les differents *timing templates* (T0 et T5) pour vous convaincre du manque de discrétion flagrant de Nmap.  



**Remarque** : Attention ! Tout acte de scan de réseau non-autorisé est
assimilé à une attaque et donc réprehensible selon la loi francaise.  Limité
vous à faire des scan des  machines  dans  la  salle  du  TP,  ou de chez vous
sur la  machine  distante `scanme.nmap.org` mise  en ligne specifiquement par
les developpeurs de l’outil à des fins pédagogiques.

<!-- idées de question https://lipn.univ-paris13.fr/~kanawati/M4210/M4210-TP1.pdf -->

<!-- *Source : https://medium.com/bugbountywriteup/intro-to-nmap-192c1796bb39* -->

- - - 
