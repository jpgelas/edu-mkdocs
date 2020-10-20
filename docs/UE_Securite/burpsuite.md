# TP Burp Suite

> Responsables : Jp Gelas, Thomas Begin

- - - 

**Objectif :** Découvrir et prendre en main les fonctionnalités de base du logiciel *Burp Suite* dédié à l’audit de sécurité web.

*Burp Suite*, souvent appelé *Burp* est un logiciel dédié à l’audit de sécurité web, utilisé par une majorité de professionnels de la sécurité de l’information.
Ses fonctions principales sont un *proxy web* et un *scanner* de vulnérabilité web. Ce logiciel est développé par l’entreprise [PortSwigger](https://portswigger.net/). Burp Suite a une version gratuite, qui inclut le *proxy*, le *repeater* et *l’intruder* (de manière limitée) que nous allons découvrir dans ce TP.

Burp est le logiciel incontournable pour auditer une application web, car il permet d'accéder aux échanges entre le navigateur et le serveur web, afin de comprendre l’architecture et le fonctionnement de la solution à auditer.

Avant de débuter le TP a proprement dit nous allons décrire les modules que nous allons utiliser pendant cette séance.

**Le proxy HTTP** 

Le module proxy HTTP de Burp est un proxy d’interception, qui permet de se placer entre l’utilisateur et les applications HTTP, afin d’intercepter toutes les requêtes émises par l’utilisateur. C’est LA fonction principale pour un pentester web, parce que cela permet une compréhension totale du fonctionnement du site web. Le proxy offre deux possibilités :

  - soit intercepter de façon bloquante les requêtes, dans le but de modifier à la volée toutes les requêtes émises par l’application web (onglet intercept) ;
  - soit placer l’outil en mode passif, et dans ce cas un historique des requêtes émises par le site sera disponible dans l’onglet HTTP history ou WebSocket history (selon le protocole de communication).

Enfin, ce module propose plusieurs fonctions de configurations dans le but de modifier de façon automatique les requêtes qu’il reçoit.

**Le répéteur HTTP**

Le *repeater* est un module envoyant des requêtes HTTP à un serveur. Il récupère les requêtes interceptées précédemment par le proxy et permet de les modifier manuellement, avant de les renvoyer individuellement au serveur.
Le *repeater* est particulièrement utilisé pour tester les failles logiques, car la précision des modifications des requêtes qu’il offre est appréciée pour analyser des processus logiques, comme le paiement d’un panier d’achats.

(source : [vaadata](https://www.vaadata.com/blog/fr/introduction-burp-outil-dedie-securite-plateformes-web/)



**TODO**




- - -
