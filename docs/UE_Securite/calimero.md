# Challenge Wifi Calimero ![Calimero](img/calimero.jpg "Calimero")


> Responsables : Jp Gelas, Thomas Begin

- - - 

## Introduction

*Objectif :* Casser en temps limité le maximum de PSK (via des attaques Wifi WPA/WPA2 mais pas que...) 

Le *Challenge Calimero* permet de vous initier au cassage de clé wifi WPA et
WPA2 pré partagées (*pre-shared key* ou *psk*) sur des réseaux Wifi prévus à
cet effet.

Les clés à casser sont celles des _Access Point_ (AP) dont les SSID diffusés
sont `calimeroN` (0 ≤ `N` ≤ 7). Il est strictement interdit de tenter de casser
les clés d’autres AP accessibles lors du challenge.

Dès qu’une clé est cassée vous devrez la saisir dans l’interface développée
spécialement pour ce challenge et accessible à l’adresse définie par les
organisateurs du challenge. 

Pensez à sélectionner le nom de votre équipe au préalable sinon les points
seront pour l’équipe adverse. 

Cette [vidéo](https://www.youtube.com/watch?v=mzQBo3a7cHc) résume brièvement (2
minutes) l’usage de l’interface (en cours de développement à l’époque).
L’interface permet non seulement de saisir les mots de passe cassé mais
également de voir l’avancement des autres équipes en temps réel.

**Remarque importante :** Vous n’aurez pas à envoyer de paquets de *DEAUTH*
pour provoquer des reconnexions et donc forcer l’apparition des *handshake
(eapol)*. Les organisateurs s’en chargent en laissant tourner un script qui
provoque des reconnexions d’une station sur chaque AP toutes les minutes
environ.

Voici quelques informations qui pourront être utiles pour casser plus
efficacement les _psk_ de chacun des AP.

## calimero0

  L’administrateur de cet AP n’est pas très au fait des problèmes de sécurité
et surtout n’a pas beaucoup d’imagination.  Essayez en priorité des mots de
passe relativement évidents (Ex : admin, password, motdepasse, 12345678,
abcdefgh, le ssid,… )

<!-- calimero0 : calimero0 -->

## calimero1

  L’administrateur a bien changé le mot de passe par défaut. Vous allez devoir
utiliser un dictionnaire (exemple : *rockyou.txt*). 
Reportez-vous à l'annexe pour un peu d'aide.
  
<!-- calimero1 : !!7676tigger7676!! -->

## calimero2

  On sait que l’administrateur préfixe toujours ses mots de passe par la chaîne
de caractères `nautibus` et qu’il fait suivre ce mot de 4 chiffres décimaux
(Exemples : *nautibus1234*, *nautibus0000*,...).  Après avoir évalué le nombre
de combinaisons possibles et la taille du fichier dictionnaire, construisez
votre propre dictionnaire (une combinaison par ligne) et utilisez le pour
craquer la PSK chiffrée que vous capturerez.

<!-- calimero2 : nautibus4242 -->


## calimero3

Une requête GET HTTP a été capturé en provenance du poste de l'administrateur.
On dirait bien une requête d'authentification. On ne peut négliger que le mot
de passe encodé qu'elle contient est peut être aussi celui de l'AP (une fois
celui-ci décodé bien sur).  

```text
...
GET / HTTP/1.1
Authorization: Basic VGhpc1Bhc3NJc0Jhc2U2NEVuY29kZWQ==
User-Agent: InsaneBrowser
Host: www.ledomaine.org
Accept: */*
...
```

<!-- calimero3 : ThisPassIsBase64Encoded -->

## calimero4

  Télécharger la trace réseau suivante [simpleauth.pcapng.gz](img/simpleauth.pcapng.gz). 
  Peut-être y a t-il des échanges de paquets contenant un mot de passe à essayer sur cette AP. 

<!-- calimero4 : NeverTrustTheNetwork -->

## calimero5

Vous avez mis la main sur un fichier de configuration d'un équipement Cisco (un
extrait ci-dessous).  Vous constatez que la plupart des mots de passe sont
chiffrés avec un *hash cisco 7* réputé faible (*i.e.* réversible car c'est une simple
obfuscation via une série de xor).  Le mot de passe cherché est celui du mode
*enable* (type 5 / md5) qui lui est incassable en un temps raisonnable avec les
ressources à votre disposition puisqu'il fait minimum 8 caractères d'après le
fichier de configuration.  Par déduction peut être allez vous quand même
découvrir le mot de passe de *enable*.

```text
!
security passwords min-length 8
no logging console
enable secret 5 $1$p8Y6$MCdRLBzuGlfOs9S.hXOp0.
!
username hub password 7 025017705B3907344E 
username admin privilege 15 password 7 10181A325528130F010D24
username guest password 7 124F163C42340B112F3830
!
line con 0
 password 7 144101205C3B29242A3B3C3927
 session-timeout 600

```


Deux liens vers un outil en ligne pour craquer les mots de passe cisco de type
7 ([lien
1](https://www.frameip.com/decrypter-dechiffrer-cracker-password-cisco-7),
[lien 2](https://packetlife.net/toolbox/type7/))

<!-- calimero5 : 6sK0_enable -->

 
## calimero6 

 L’administrateur n'a peut être pas changé le mot de passe par défaut sachant
que l'AP que vous attaquez est un *Micronet - Access Point - SP912* (ce ne sera vrai que pour le contexte
de ce challenge).

<!-- calimero6 : default -->

## calimero7

Analysez la trace réseau *pcap* disponible [ici](img/havefun.pcapng.gz). Elle contient le mot de passe requis.

<!-- calimero7 : OhLaBelleImage -->


---

## Annexe

Quelques rappels sur les outils et la démarche à mettre en œuvre.

Nous vous recommandons d’utiliser la suite *aircrack-ng*.

  1. Mettre sa carte Wifi en mode Monitor (si elle le supporte) avec `airmon-ng`
  1. Observer le trafic Wifi et sélectionner le canal sur lequel l’AP opère avec `airodump-ng` 
  1. Capturer le trafic jusqu’à obtenir le *handshake*. Sauvegarder le tout dans un fichier `.cap`.
  1. (optionnel) Vérifier la présence du handshake en ouvrant votre fichier de capture avec Wireshark (filtre : `eapol`), puis sauvegarder uniquement les paquets utiles dans un nouveau fichier de capture afin de pouvoir vous débarrasser du fichier de capture initial qui peut être relativement volumineux. 
  1. Lancer `aircrack-ng` pour réaliser une attaque par dictionnaire (Ex : rockyou.txt) pour déterminer le mot de passe (la psk).
  1. (optionnel) Essayez de vous connecter à l’AP avec la PSK trouvée pour valider que c’est la bonne.
  1. Saisir la PSK dans l’interface du challenge pour qu’elle soit validée par les organisateurs et montrer aux autres équipes comment vous assurez trop bien !


Commandes de base    | Description
------------- | -------------
`ifconfig -a` | Lister les interfaces réseau
`airmon-ng start wlan0` | Passer l’interface en mode monitor (change wlan0 en wlan0mon)
`airodump-ng wlan0mon` | Observer le trafic sur tous les canaux 
`airodump-ng -w capture_file -c [channel number] wlan0mon` | Capturer le trafic sur un canal donné
`aircrack-ng -w [password_list_file].txt [capture_file].cap` | Casser la PSK 


- - -



















