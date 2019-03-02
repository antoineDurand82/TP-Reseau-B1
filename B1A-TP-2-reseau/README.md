# B1 Réseau 2018 - TP2

## Exploration locale en solo

### Affichage d'informations sur la pile TCP/IP locale

**Interface Wifi :**


Nom : Intel(R) Wireless-AC 9462

Adresse Mac : 04-D3-B0-0E-73-A1

Adresse IP : 10.33.3.91 = 0000 1010 . 0010 0001 . 0000 0011 . 0101 1011

Masque : 255.255.252.0/22 = 1111 1111 . 1111 1111 . 1111 1100 . 0000 0000

Adresse réseau : 10.33.0.0/22 = 0000 1010. 0010 0001 . 0000 0000 . 0000 0000

Adresse broadcast : 10.33.3.255/22 = 0000 1010  . 0010 0001 . 0000 0011 . 1111 1111



**Interface Ethernet :**


Nom : Qualcomm Atheros AR8171/8175 PCI-E

Adresse Mac : 30-9C-23-FD-6E-33

Adresse IP : Inexistante, je suis en Wi-fi

Adresse réseau : inexistante

Adresse broadcast : inexistante



**Affichage de la gateway :**


ipconfig/all (dans le cmd) et il faut aller voir sur l'adresse par defaut.

Sur Windows, pour afficher votre ip ou bien l'adresse mac ou encore la gateway de votre carte wifi:

+ Faite un click droit sur votre icone de wifi en bas à droite de votre écran
+ Cliquer sur "Ouvrir les paramètres réseau et internet"
+ Sélectionner "Centre réseau et partage" en bas de votre écran
+ Ensuite sur "Modifierles paramètres de la carte" en haut à gauche
+ Puis terminer, click droit sur votre carte wifi active et faite "Status" puis "Détails"

La gateway dans le réseau d'Ingésup permet aux utilisateurs du réseau d'Ingésup d'accéder à internet.


### Modifications des informations

Première adresse ip du réseau: 10.33.0.0
Dernière adresse ip du réseau (possible, pas forcément disponible) : 10.33.3.255  
<br>
Pour changer l'adresse IP de votre carte WIFI vous aurez besoin de votre adresse ip actuel, de votre masque de réseau ainsi que votre passerelle par défaut que vous aurez récupérer au préalable grâce à la manipulation qui est décrit juste au dessus.

Pour la modifié vous suivrez la manipulation au-dessus jusqu'au click droit sur votre wifi

Là vous sélectionnez "Propriétés", vous clickez sur "Protocole internet version 4 (TCP/IPV4)"

Clicker sur "propriétés" puis sélectionnez "utilisez l'adresse IP suivante"

Rentrez votre masque de sous-réseau, votre passerelle par défaut

Et enfin rentrer votre nouvelle adresse IP

#### NMAP
#### Modification d'adresse IP - pt 2

Pour le réseau de ynov si vous voulez changer votre adresse IP avec une adresse dispo
Allez sur nmap, faite ```nmap -sn -PE  "votre adresse de réseau avec votre masque"``` dans votre cmd
Regardez 2 adresses utilisé qui ont un écart entre elles et modifié votre adresse IP comme nous avons vue au dessus avec l'adresse que vous sélectionnez.


## Exploration locale en duo
### Modif adresse ip

Nous avons pu nous ping en étant en cable éthernet:
```
Envoi d'une requête 'Ping' 192.168.1.1 avec 32 octects de données:
Réponse de 192.168.1.1 : octets=32 temps<1ms TTL=128
Réponse de 192.168.1.1 : octets=32 temps<1ms TTL=128
Réponse de 192.168.1.1 : octets=32 temps<1ms TTL=128
Réponse de 192.168.1.1 : octets=32 temps<1ms TTL=128
```

### Utilisation d'un des deux comme gateway
L'un des deux 2 pc a activer son wi-fi et a enlevé sa passerelle par défaut sur sa carte ethernet
Il click droit sur sa carte wi-fi, sélectionne partage et click sur "Autoriser d'autres utilisateurs du réseau à se connecter via la connexion internet de cet ordinateur"
Le deuxième pc entre l'adresse ip de la carte ethernet du premier pc en tant que passerelle, il entre l'adresse suivente dans le DNS ``` 8.8.8.8 ```ensuite fait un test sur son cmd avec la commande suivant ```nslookup google.com ``` et obtient ceci comme réponse: 
```
 Serveur :   google-public-dns-a.google.com
Address:  8.8.8.8

Réponse ne faisant pas autorité :
Nom :    google.com
Addresses:  2a00:1450:4007:817::200e
216.58.206.238
``` 