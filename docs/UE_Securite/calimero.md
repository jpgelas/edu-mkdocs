# Challenge Wifi Calimero

> Responsables : Jp Gelas, Thomas Begin

- - - 


**Objectif :** Casser en temps limité le maximum de PSK (Attaques Wifi WPA/WPA2)

![Calimero](img/calimero.jpg "Calimero")

Le *Challenge Calimero* permet de vous initier au cassage de clé wifi WPA et WPA2 pré partagées (*pre-shared key* ou *psk*) sur des réseaux Wifi prévus à cet effet.

Les clés à casser sont celles des _Access Point_ (AP) dont les SSID diffusés sont `calimeroN` (0 ≤ `N` ≤ 4). Il est strictement interdit de tenter de casser les clés d’autres AP accessibles lors du challenge.

Dès qu’une clé est cassée vous devrez la saisir dans l’interface développée spécialement pour ce challenge et accessible à l’adresse définie par les organisateurs du challenge. 

Pensez à sélectionner le nom de votre équipe au préalable sinon les points seront pour l’équipe adverse. 

Cette [vidéo](https://www.youtube.com/watch?v=mzQBo3a7cHc) résume brièvement (2 minutes) l’usage de l’interface (en cours de développement à l’époque). L’interface permet non seulement de saisir les mots de passe cassé mais également de voir l’avancement des autres équipes en temps réel.

**Indices :** Voici quelques informations qui pourront être utiles pour casser plus efficacement les _psk_ de chacun des AP.

  - **calimero0** : l’administrateur de cet AP n’est pas très au fait des problèmes de sécurité et surtout n’a pas beaucoup d’imagination. Essayez en priorité des mots de passe relativement évidents (Ex : admin, password, motdepasse, 12345678, abcdefgh, le ssid,… )
  - **calimero1** : l’administrateur a bien changé le mot de passe par défaut. Vous allez devoir utiliser un dictionnaire (Ex : rockyou.txt)
  - **calimero2** : l’administrateur a bien changé le mot de passe par défaut. Vous allez devoir utiliser un dictionnaire.
  - **calimero3** : l’administrateur a bien changé le mot de passe par défaut. Vous allez devoir utiliser un dictionnaire.
  - **calimero4** : on sait que l’administrateur préfixe toujours ses mots de passe par la chaîne de caractères `nautibus` qu’il fait suivre de 4 chiffres (Ex : `nautibus1234`). Après avoir évalué le nombre de combinaisons possibles et la taille du fichier dictionnaire, construisez votre propre dictionnaire (une combinaison par ligne).

**Remarque importante :** Vous n’aurez pas à envoyer de paquets de *DEAUTH* pour provoquer des reconnexions et donc forcer l’apparition des *handshake (eapol)*. Les organisateurs s’en chargent en laissant tourner un script qui provoque des reconnexions d’une station sur chaque AP toutes les minutes environ.

**Rappels :** Quelques rappels sur les outils et la démarche à mettre en œuvre.

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



















