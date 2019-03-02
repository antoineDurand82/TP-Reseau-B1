# B1-Reseau-2018-tp3

## Création et utilisation simples d'une VM CentOS

### I.Configuration réseau d'une machine CentOS

Pour prouver que j'ai internet depuis la VM, je fais: ```curl -SL www.google.com``` et je me retrouve avec des lignes infames qui prouvent que la page html de google a bien été lu.

#### Ping

Pour prouver que mon pc hôte et ma vm peuvent communiquer, je les ping entre elles:

De la machine au pc hôte:

```
[antoine@localhost selinux]$ ping 10.33.2.145
PING 10.33.2.145 (10.33.2.145) 56(84) bytes of data.
64 bytes from 10.33.2.145: incmp_seq=1 ttl=127 time=0.430 ms
64 bytes from 10.33.2.145: incmp_seq=2 ttl=127 time=0.564 ms
64 bytes from 10.33.2.145: incmp_seq=3 ttl=127 time=0.483 ms
```

De l'hôte jusqu'à la machine:

```
PS C:\Users\antoi> ping 192.168.127.10

Envoi d’une requête 'Ping'  192.168.127.10 avec 32 octets de données :
Réponse de 192.168.127.10 : octets=32 temps<1ms TTL=128
Réponse de 192.168.127.10 : octets=32 temps<1ms TTL=128
Réponse de 192.168.127.10 : octets=32 temps<1ms TTL=128
```
#### Table de routage

Pour la table de routage depuis la VM il faut faire ```ip route```

Cette commande nous affiche ceci:
```
default via 10.0.2.2 dev enp0s3 proto dhcp metric 100
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 100
192.168.127.0/24 dev enp0s3 proto kernel scope link src 192.168.127.10 metric 101
```

Pour la table de routage depuis l'hôte il faut faire ```route print -4```, le -4 étant la pour rester uniquement sur les ports ipv4

On obtient ceci

```
===========================================================================
Liste d'Interfaces
 18...30 9c 23 fd 6e 33 ......Qualcomm Atheros AR8171/8175 PCI-E Gigabit Ethernet Controller (NDIS 6.30)
 17...0a 00 27 00 00 11 ......VirtualBox Host-Only Ethernet Adapter #2
 14...04 d3 b0 0e 73 a2 ......Microsoft Wi-Fi Direct Virtual Adapter
 13...06 d3 b0 0e 73 a1 ......Microsoft Wi-Fi Direct Virtual Adapter #2
 15...04 d3 b0 0e 73 a1 ......Intel(R) Wireless-AC 9462
  1...........................Software Loopback Interface 1
===========================================================================

IPv4 Table de routage
===========================================================================
Itinéraires actifs :
Destination réseau    Masque réseau  Adr. passerelle   Adr. interface Métrique
          0.0.0.0          0.0.0.0      10.33.3.253      10.33.2.145     45
        10.33.0.0    255.255.252.0         On-link       10.33.2.145    301
      10.33.2.145  255.255.255.255         On-link       10.33.2.145    301
      10.33.3.255  255.255.255.255         On-link       10.33.2.145    301
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
    192.168.127.0    255.255.255.0         On-link     192.168.127.1    281
    192.168.127.1  255.255.255.255         On-link     192.168.127.1    281
  192.168.127.255  255.255.255.255         On-link     192.168.127.1    281
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     192.168.127.1    281
        224.0.0.0        240.0.0.0         On-link       10.33.2.145    301
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     192.168.127.1    281
  255.255.255.255  255.255.255.255         On-link       10.33.2.145    301
===========================================================================
```


Nous pouvons voir que dans les 2 tables de routages, le réseau 192.168.127.0 est présent
Ici sur l'hôte:
```
192.168.127.0    255.255.255.0         On-link     192.168.127.1    281
```
et là sur la VM:
```
192.168.127.0/24 dev enp0s3 proto kernel scope link src 192.168.127.10 metric 101
```
J'en déduit donc qu'il s'agit de la ligne qui leur permet de discuter via le réseau host-only


#### Utilisation de dig

Pour connaître l'adresse IP de google avec dig:

```
[root@localhost ~]# dig www.google.com
```
Et on obtient 
```
;; Query time: 5 msec
;; SERVER: 10.33.10.20#53(10.33.10.20)
;; WHEN: Tue Jan 08 13:55:16 CET 2019
;; MSG SIZE  rcvd: 855
```

Donc l'ip de google est ```10.33.10.20```

En sachant que le ping de ynov.com nous donne la même adresse ip.

Je reste donc sur une incompréhension.


### II.Notion de ports et SSH

#### Exploration des ports locaux

Grace à la commande ```ss -l4 -n -p``` on connait donc l'utilisateur du port 22 comme demandé:

```
Netid   State       Recv-Q  Send-Q          Local Adresse:Port      Peer Address:Port
tcp     LISTEN     0       128             *:22                    *:*
user:(("sshd",pid=3159,fd=3))
```

#### SSH

Pour pouvoir utiliser le powershell de notre VM depuis notre powershell du pc hôte:
Il faut être sur de pouvoir ping note VM depuis l'hôte et faire tous simplement:
```ssh root@ipv4DeLaVM ``` donc dans notre cas dans le tp ```ssh root@192.168.127.10```
Rentrer notre mdp root de la VM et voilà, nous pouvons utiliser le pwershell de l'hôte pour notre VM

#### Firewall

##### A. SSH:

Pour vérifier que mon port sshd est bien modifié je refais la manip suivante ```ss -l4 -n -p```
et je me retrouve avec:
```
Netid   State       Recv-Q  Send-Q          Local Adresse:Port      Peer Address:Port
tcp     LISTENT     0       128             *:2222                    *:*
user:(("sshd",pid=11544,fd=3))
```
Mon port sshd a donc bien été modifié

Je ne peux donc pas connecter un nouveau powershell de mon hôte au powershell de ma VM, j'obtient le message suivant:
```ssh: connect to host 192.168.127.10 port 22: Connection refused```

Si l'on veut pouvoir se conecter au port ssh il faut soit:<br>
    * Modifié notre fichier de config sshd dans la VM pour le repasser en 22 et restart le sshd avec ```systemctl restart sshd```<br>
    * Ou bien modifié le fichier de config sshd du pc hôte pour passer en 2222<br>
    * Ou encore on va sur notre VM, on autorise les connexions sur le port TCP80 avec ```firewall-cmd --add-port=80/tcp --permanent```<br>On active le tout avec la commande ```--reload```<br>Pour finir sur notre powershell hôte on met la commande suivante ```ssh -p N°port root@ipv4DeLaVM``` donc ici ```ssh -p 80 root@192.168.127.10```

##### B. Netcat

On a installer netcat avec la commande ```yum install nc```
De la on a suivit le tp avec la création des 3 powershells et voii ce que l'on obtient à la fin:
```
Netid       Stat        Recv-Q      Send-Q                Local Address:Port             Peer Address:Port
tcp         LISTEN          0         10                      *:5454                           *:*                   users:(("nc",pid=11752,fd=4))
```

### III.Routage statique
#### 1. Préparation des hôtes 
##### Préparation avec câble  
+ IP PC1 : 192.168.112.1/30  
+ IP PC2 : 192.168.112.2/30  
+ Vérifier que les deux PC se ping :  
  
##### Préparation VirtualBox  
Modifier vos réseaux host-only pour qu'ils soient :

+ PC1 : réseau 1 : 192.168.101.0/24  
+ PC2 : réseau 2 : 192.168.102.0/24  

Créez (ou réutilisez) une VM, et modifiez son adresse :
+ VM1 (sur PC1) : 192.168.101.10
+ VM2 (sur PC2) : 192.168.102.10  

##### Check
Assurez vous que :

+ PC1 et PC2 se ping en utilisant le réseau 12  
```
C:\Users\theod>ping 192.168.112.2

Envoi d’une requête 'Ping'  192.168.112.2 avec 32 octets de données :
Réponse de 192.168.112.2 : octets=32 temps=7 ms TTL=128
Réponse de 192.168.112.2 : octets=32 temps=7 ms TTL=128

Statistiques Ping pour 192.168.112.2:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 7ms, Maximum = 7ms, Moyenne = 7ms
#### Préparation VirtualBox  
```

```
PS C:\Users\antoi> ping 192.168.112.1
 
Envoi d’une requête 'Ping'  192.168.112.1 avec 32 octets de données :
Réponse de 192.168.112.1 : octets=32 temps=5 ms TTL=128
Réponse de 192.168.112.1 : octets=32 temps=5 ms TTL=128
 
Statistiques Ping pour 192.168.112.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 5ms, Maximum = 5ms, Moyenne = 5ms
```  
+ VM1 et PC1 se ping en utilisant le réseau 1  

VM1 vers PC1 :  
```
[root@localhost ~]# ping 192.168.112.1
PING 192.168.112.1 (192.168.112.1) 56(84) bytes of data.
64 bytes from 192.168.112.1: icmp_seq=1 ttl=127 time=0.728 ms
64 bytes from 192.168.112.1: icmp_seq=2 ttl=127 time=1.25 ms
--- 192.168.112.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3002ms
rtt min/avg/max/mdev = 0.728/1.124/1.370/0.245 ms
```
PC1 vers VM1 :  
```
C:\Users\theod>ping 192.168.101.10

Envoi d’une requête 'Ping'  192.168.101.10 avec 32 octets de données :
Réponse de 192.168.101.10 : octets=32 temps<1ms TTL=64
Réponse de 192.168.101.10 : octets=32 temps<1ms TTL=64

Statistiques Ping pour 192.168.101.10:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms
```

+ VM2 et PC2 se ping en utilisant le réseau 2  

VM2 vers PC2 :   
```
[root@localhost ~]# ping 192.168.112.2
PING 192.168.112.2 (192.168.112.2) 56(84) bytes of data.
64 bytes from 192.168.112.2: icmp_seq=1 ttl=127 time=1.12 ms
64 bytes from 192.168.112.2: icmp_seq=2 ttl=127 time=1.28 ms
--- 192.168.112.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2007ms
rtt min/avg/max/mdev = 1.124/1.217/1.288/0.079 ms
```
PC2 vers VM2 :
 
```
PS C:\Users\antoi> ping 192.168.102.10
 
Envoi d’une requête 'Ping'  192.168.102.10 avec 32 octets de données :
Réponse de 192.168.102.10 : octets=32 temps=5 ms TTL=128
Réponse de 192.168.102.10 : octets=32 temps=4 ms TTL=128
 
Statistiques Ping pour 192.168.102.10:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 4ms, Maximum = 5ms, Moyenne = 4ms
```
Observez vos tables de routage sur tous les hôtes, pour comprendre : 
+ PC1 :
``` 
C:\Users\theod>route print -4
IPv4 Table de routage
===========================================================================
Itinéraires actifs :
Destination réseau    Masque réseau  Adr. passerelle   Adr. interface Métrique
    192.168.101.0    255.255.255.0         On-link     192.168.101.1    281
    192.168.101.1  255.255.255.255         On-link     192.168.101.1    281
  192.168.101.255  255.255.255.255         On-link     192.168.101.1    281
    192.168.112.0  255.255.255.252         On-link     192.168.112.1    281
    192.168.112.1  255.255.255.255         On-link     192.168.112.1    281
    192.168.112.3  255.255.255.255         On-link     192.168.112.1    281
===========================================================================
Itinéraires persistants :
  Aucun
```  
+ VM1 :  
```
[root@localhost ~]# ip route
default via 10.0.2.2 dev enp0s3 proto dhcp metric 100
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 100
192.168.101.0/24 dev enp0s8 proto kernel scope link src 192.168.101.10 metric 101
```  

+ PC2 :  
```
PS C:\Users\antoi> route print -4
IPv4 Table de routage
===========================================================================
Itinéraires actifs :
Destination réseau    Masque réseau  Adr. passerelle   Adr. interface Métrique
    192.168.102.0    255.255.255.0         On-link     192.168.102.2    281
    192.168.102.2  255.255.255.255         On-link     192.168.102.2    281
  192.168.102.255  255.255.255.255         On-link     192.168.102.2    281
    192.168.112.0  255.255.255.252         On-link     192.168.112.2    281
    192.168.112.2  255.255.255.255         On-link     192.168.112.2    281
    192.168.112.3  255.255.255.255         On-link     192.168.112.2    281
===========================================================================
Itinéraires persistants :
  Aucun
```
+ VM2 :  
```
[root@localhost ~]# ip route
default via 10.0.2.2 dev enp0s3 proto dhcp metric 100
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 100
192.168.102.0/24 dev enp0s8 proto kernel scope link src 192.168.102.10 metric 101
```  

##### Activation du routage sur les PCs  
+ modifier la clé registre HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\ Services\Tcpip\Parameters\IPEnableRouter en passant sa valeur de 0 à 1  
+ activer le service de routage ("lancer l'application de routage")
    + Win + R  
    + services.msc > Entrée  
    + Naviguer à "Routage et accès distant"  
        + Clic-droit > Propriétés  
        + Pour le démarrage, sélectionner "Automatique"  
        + Appliquer  
        + Démarrer  

#### 2. Configuration du routage  
Récapitulatif :  
+ PC1 : 192.168.112.1/30  
+ VM : 192.168.101.10/24  
+ PC2 : 192.168.112.2/30  
+ VM2 : 192.168.102.10/24  
##### PC1  
+ PC1 accède déjà aux réseaux 1 et 12, il faut juste lui dire comment accéder au réseau 2  
```route add 192.168.102.0 mask 255.255.255.0 192.168.112.2```  
PC1 ping correctement PC2 dans le réseau 2  

+ PC2 vers PC1 dans le réseau 2  
`route add 192.168.101.0 mask 255.255.255.0 192.168.112.1`  
PC2 ping correctement PC1 dans le réseau 2  
