# TP3 : 32°13'34"N 95°03'27"W

## Sommaire

- [TP3 : 32°13'34"N 95°03'27"W](#tp3--321334n-950327w)
  - [Sommaire](#sommaire)
- [I. ARP basics](#i-arp-basics)
- [II. ARP dans un réseau local](#ii-arp-dans-un-réseau-local)
  - [1. Basics](#1-basics)
  - [2. ARP](#2-arp)
  - [3. Bonus : ARP poisoning](#3-bonus--arp-poisoning)


## I. ARP basics

☀️ Avant de continuer...
 - affichez l'adresse MAC de votre carte WiFi !

 ```
PS C:\Users\kevin> ipconfig /all
 ```
  ```
Carte Ethernet Ethernet :

   Statut du média. . . . . . . . . . . . : Média déconnecté
   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Killer E2500 Gigabit Ethernet Controller
   Adresse physique . . . . . . . . . . . : 00-D8-61-84-4D-53
   DHCP activé. . . . . . . . . . . . . . : Non
   Configuration automatique activée. . . : Oui
 ```
  ```
  Adresse physique . . . . . . . . . . . : 00-D8-61-84-4D-53
 ```

☀️ Affichez votre table ARP
- allez vous commencez à devenir grands, je vous donne pas la commande, demande à m'sieur internet !

```
PS C:\Users\kevin> arp -a
```
```
Interface : 10.33.79.10 --- 0xe
  Adresse Internet      Adresse physique      Type
  10.33.65.63           44-af-28-c3-6a-9f     dynamique
  10.33.66.78           e4-b3-18-48-36-68     dynamique
  10.33.73.77           98-8d-46-c4-fa-e5     dynamique
  10.33.77.160          c8-94-02-f8-ab-97     dynamique
  10.33.79.254          7c-5a-1c-d3-d8-76     dynamique
  10.33.79.255          ff-ff-ff-ff-ff-ff     statique
  224.0.0.2             01-00-5e-00-00-02     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique
```

☀️ Déterminez l'adresse MAC de la passerelle du réseau de l'école

- la passerelle, vous connaissez son adresse IP normalement (cf TP1 ;) )
- si vous avez un accès internet, votre PC a forcément l'adresse MAC de la passerelle dans sa table ARP

```
PS C:\Users\kevin> arp -a 10.33.79.254
```
```
Interface : 10.33.79.10 --- 0xe
  Adresse Internet      Adresse physique      Type
  10.33.79.254          7c-5a-1c-d3-d8-76     dynamique
```

☀️ Supprimez la ligne qui concerne la passerelle
- une commande pour supprimer l'adresse MAC de votre table ARP
- si vous ré-affichez votre table ARP, y'a des chances que ça revienne presque tout de suite !
```
PS C:\WINDOWS\system32> arp -d 10.33.79.254
```

☀️ Prouvez que vous avez supprimé la ligne dans la table ARP
- en affichant la table ARP
- si la ligne est déjà revenue, déconnecte-toi temporairement du réseau de l'école, et supprime-la de nouveau

Imédiatement après le delete :

```
PS C:\WINDOWS\system32> arp -a
Interface : 10.33.79.10 --- 0xe
  Adresse Internet      Adresse physique      Type
  10.33.65.63           44-af-28-c3-6a-9f     dynamique
  10.33.66.78           e4-b3-18-48-36-68     dynamique
  10.33.73.77           98-8d-46-c4-fa-e5     dynamique
  10.33.77.160          c8-94-02-f8-ab-97     dynamique
  10.33.79.255          ff-ff-ff-ff-ff-ff     statique
  224.0.0.2             01-00-5e-00-00-02     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
```

3 Secondes après le delete :

```
PS C:\WINDOWS\system32> arp -a
Interface : 10.33.79.10 --- 0xe
  Adresse Internet      Adresse physique      Type
  10.33.65.63           44-af-28-c3-6a-9f     dynamique
  10.33.66.78           e4-b3-18-48-36-68     dynamique
  10.33.73.77           98-8d-46-c4-fa-e5     dynamique
  10.33.77.160          c8-94-02-f8-ab-97     dynamique
  | 10.33.79.254          7c-5a-1c-d3-d8-76     dynamique |
  10.33.79.255          ff-ff-ff-ff-ff-ff     statique
  224.0.0.2             01-00-5e-00-00-02     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  255.255.255.255       ff-ff-ff-ff-ff-ff     statique
```
☀️ Wireshark
- capture arp1.pcap

- lancez une capture Wireshark, puis supprimez la ligne de la passerelle dans la table ARP pendant que la capture est en cours
- la capture doit contenir uniquement 2 trames :

     - un ARP request que votre PC envoie pour apprendre l'adresse MAC de la passerelle
    - et la réponse

[Capture Wireshark](#arp1.pcap)


## II. ARP dans un réseau local

### 1. Basics

☀️ Déterminer
- pour la carte réseau impliquée dans le partage de connexion (carte WiFi ?)
- son adresse IP au sein du réseau local formé par le partage de co
- son adresse MAC
```
ipconfig /all
```
```
Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Killer(R) Wireless-AC 1550i Wireless Network Adapter (9560NGW) 160MHz
   Adresse physique . . . . . . . . . . . : A8-6D-AA-E7-87-40
   DHCP activé. . . . . . . . . . . . . . : Oui
   Configuration automatique activée. . . : Oui
   Adresse IPv6. . . . . . . . . . . . . .: 2a01:cb06:9024:9383:7488:49ba:7db3:1899(préféré)
   Adresse IPv6 temporaire . . . . . . . .: 2a01:cb06:9024:9383:b08b:e3c5:b966:74e1(préféré)
   Adresse IPv6 de liaison locale. . . . .: fe80::1820:2585:92e9:c415%14(préféré)
   Adresse IPv4. . . . . . . . . . . . . .: 172.20.10.6(préféré)
   Masque de sous-réseau. . . . . . . . . : 255.255.255.240
   Bail obtenu. . . . . . . . . . . . . . : mardi 8 octobre 2024 11:10:18
   Bail expirant. . . . . . . . . . . . . : mardi 8 octobre 2024 12:10:17
   Passerelle par défaut. . . . . . . . . : fe80::881e:5aff:fea3:4b64%14
                                       172.20.10.1
   Serveur DHCP . . . . . . . . . . . . . : 172.20.10.1
   IAID DHCPv6 . . . . . . . . . . . : 128478634
   DUID de client DHCPv6. . . . . . . . : 00-01-00-01-2D-FF-23-5F-00-D8-61-84-4D-53
   Serveurs DNS. . .  . . . . . . . . . . : fe80::881e:5aff:fea3:4b64%14
                                       172.20.10.1
   NetBIOS sur Tcpip. . . . . . . . . . . : Activé
   ```
   ```
   Adresse physique . . . . . . . . . . . : A8-6D-AA-E7-87-40
   Adresse IPv4. . . . . . . . . . . . . .: 172.20.10.6(préféré) 
   ```

☀️ DIY
- changer d'adresse IP

- vous pouvez le faire en interface graphique


- faut procéder comme au TP1 :

    - vous respectez les informations que vous connaissez du réseau
    - remettez la même passerelle, le même masque, et le même serveur DNS
    - changez seulement votre adresse IP, ne changez que le dernier nombre

- prouvez que vous avez bien changé d'IP

    - avec une commande !

```
PS C:\WINDOWS\system32> ipconfig
```
```
Carte réseau sans fil Wi-Fi :

   Suffixe DNS propre à la connexion. . . :
   Adresse IPv4. . . . . . . . . . . . . .: 172.20.10.9
   Masque de sous-réseau. . . . . . . . . : 255.255.255.240
   Passerelle par défaut. . . . . . . . . : 172.20.10.1
```
☀️ Pingz !

- vérifiez que vous pouvez tous vous ping avec ces adresses IP
- vérifiez avec une commande ping que vous avez bien un accès internet

```
PS C:\WINDOWS\system32> ping 172.20.10.12

Envoi d’une requête 'Ping'  172.20.10.12 avec 32 octets de données :
Réponse de 172.20.10.12 : octets=32 temps=41 ms TTL=64
Réponse de 172.20.10.12 : octets=32 temps=134 ms TTL=64
Réponse de 172.20.10.12 : octets=32 temps=178 ms TTL=64
Réponse de 172.20.10.12 : octets=32 temps=86 ms TTL=64
```
```
PS C:\WINDOWS\system32> ping 172.20.10.11

Envoi d’une requête 'Ping'  172.20.10.11 avec 32 octets de données :
Réponse de 172.20.10.11 : octets=32 temps=29 ms TTL=128
Réponse de 172.20.10.11 : octets=32 temps=14 ms TTL=128
Réponse de 172.20.10.11 : octets=32 temps=28 ms TTL=128
Réponse de 172.20.10.11 : octets=32 temps=14 ms TTL=128

Statistiques Ping pour 172.20.10.11:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 14ms, Maximum = 29ms, Moyenne = 21ms
```
```
PS C:\WINDOWS\system32> ping google.com

Envoi d’une requête 'ping' sur google.com [142.250.179.110] avec 32 octets de données :
Réponse de 142.250.179.110 : octets=32 temps=35 ms TTL=59
Réponse de 142.250.179.110 : octets=32 temps=56 ms TTL=59
Réponse de 142.250.179.110 : octets=32 temps=55 ms TTL=59
Réponse de 142.250.179.110 : octets=32 temps=42 ms TTL=59

Statistiques Ping pour 142.250.179.110:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 35ms, Maximum = 56ms, Moyenne = 47ms
```

### 2. ARP

☀️ Affichez votre table ARP !
- normalement, après les ping, vous avez appris l'adresse MAC de tous les autres
```
PS C:\WINDOWS\system32> arp -a
```
```
Interface : 172.20.10.9 --- 0xe
  Adresse Internet      Adresse physique      Type
  172.20.10.1           8a-1e-5a-a3-4b-64     dynamique
  172.20.10.11          e4-0d-36-2f-88-9d     dynamique
  172.20.10.12          50-a6-d8-9b-0b-a7     dynamique
  172.20.10.15          ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  ```

☀️ Capture arp2.pcap
- lancez tous Wireshark
- videz tous vos tables ARP
- normalement, y'a presque pas de raisons que vos PCs se contactent entre eux spontanément donc les tables ARP devraient rester vides tant que vous faites rien
- à tour de rôle, envoyez quelques ping entre vous
- constatez les messages ARP spontanés qui précèdent vos ping

    - ARP request

        - envoyé en broadcast ff:ff:ff:ff:ff:ff
        - tout le monde les reçoit donc !


    - ARP reply

        - celui qui a été ping répond à celui qui a initié le `pingv
        - il l'informe que l'adresse IP qui a été ping correspond à son adresse MAC

[Capture Wireshark](#arp2.pcap)

### 3. Bonus : ARP poisoning

⭐ Empoisonner la table ARP de l'un des membres de votre réseau
- il faut donc forcer l'injection de fausses informations dans la table ARP de quelqu'un d'autre
- on peut le faire en envoyant des messages ARP que l'on a forgé nous-mêmes
- avec quelques lignes de code, ou y'a déjà ce qu'il faut sur internet
- faites vos recherches, demandez-moi si vous voulez de l'aide
- affichez la table ARP de la victime une fois modifiée dans le compte-rendu

```
PS C:\WINDOWS\system32> arp -a
```
```
Interface : 172.20.10.9 --- 0xe
  Adresse Internet      Adresse physique      Type
  172.20.10.11          e4-0d-36-2f-88-9d     dynamique
```
⭐ Mettre en place un MITM

- MITM pour Man-in-the-middle
- placez vous entre l'un des membres du réseau, et la passerelle
- ainsi, dès que ce PC va sur internet, c'est à vous qu'il envoie tous ses messages
- pour ça, il faut continuellement empoisonner la table ARP de la victime, et celle de la passerelle


On peut donc intercepter les données de login a un site (ex: https://github.com/alandau/arpspoof) avec **Wireshark** si le site est en HTTP

[Capture Wireshark des packets HTTP](arppoisoning.pcap)
