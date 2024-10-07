# TP 1 Réseaux

## I. Récolte d'informations

### 🌞 Adresses IP de ta machine
commande :   

ipconfig 

résulat:


Carte Ethernet Ethernet :

   Statut du média. . . . . . . . . . . . : Média déconnecté
   
   Suffixe DNS propre à la connexion. . . :

Carte réseau sans fil Wi-Fi :

   Adresse IPv4. . . . . . . . . . . . . .: 10.33.79.10

### 🌞 Si t'as un accès internet normal, d'autres infos sont forcément dispos...
commande :

ipconfig /all

résulat:


Carte réseau sans fil Wi-Fi :

   Passerelle par défaut. . . . . . . . . : 10.33.79.254
   
   Serveur DHCP . . . . . . . . . . . . . : 10.33.79.254

Serveurs DNS. . .  . . . . . . . . .  : 8.8.8.8 
                                            1.1.1.1

### 🌟 BONUS : Détermine s'il y a un pare-feu actif sur ta machine
commande :

Get-NetFirewallProfile

résulat:


Name                            : Domain
Enabled                         : True

Name                            : Private
Enabled                         : True

Name                            : Public
Enabled                         : True


## II. Utiliser le réseau

### 🌞 Envoie un ping vers...

#### toi-même ! 
commande :

 ping 10.33.79.10

résulat:

 Envoi d’une requête 'Ping'  10.33.79.10 avec 32 octets de données :

Réponse de 10.33.79.10 : octets=32 temps<1ms TTL=128

Réponse de 10.33.79.10 : octets=32 temps<1ms TTL=128

Réponse de 10.33.79.10 : octets=32 temps<1ms TTL=128

Réponse de 10.33.79.10 : octets=32 temps<1ms TTL=128

Statistiques Ping pour 10.33.79.10:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms

#### vers l'adresse IP 127.0.0.1
commande :

ping 127.0.0.1

résulat:


Envoi d’une requête 'Ping'  127.0.0.1 avec 32 octets de données :

Réponse de 127.0.0.1 : octets=32 temps<1ms TTL=128

Réponse de 127.0.0.1 : octets=32 temps<1ms TTL=128

Réponse de 127.0.0.1 : octets=32 temps<1ms TTL=128

Réponse de 127.0.0.1 : octets=32 temps<1ms TTL=128

Statistiques Ping pour 127.0.0.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms

### 🌞 On continue avec ping. Envoie un ping vers...

#### ta passerelle

ping 10.33.79.254

résulat:


Envoi d’une requête 'Ping'  10.33.79.254 avec 32 octets de données :

Délai d’attente de la demande dépassé.

Délai d’attente de la demande dépassé.

Délai d’attente de la demande dépassé.

Statistiques Ping pour 10.33.79.254:
    Paquets : envoyés = 3, reçus = 0, perdus = 3 (perte 100%),

#### un(e) pote sur le réseau

*ping un pote windows*

commande :

ping 10.33.78.252

résulat:

Envoi d’une requête 'Ping'  10.33.78.252 avec 32 octets de données :

Délai d’attente de la demande dépassé.

Délai d’attente de la demande dépassé.

Délai d’attente de la demande dépassé.

Statistiques Ping pour 10.33.78.252:
    Paquets : envoyés = 3, reçus = 0, perdus = 3 (perte 100%),

*ping un pote Mac*

commande :

ping 10.33.78.96

résultat :

Envoi d’une requête 'Ping'  10.33.78.96 avec 32 octets de données :

Réponse de 10.33.78.96 : octets=32 temps=98 ms TTL=64

Réponse de 10.33.78.96 : octets=32 temps=96 ms TTL=64

Réponse de 10.33.78.96 : octets=32 temps=112 ms TTL=64

Réponse de 10.33.78.96 : octets=32 temps=120 ms TTL=64

Statistiques Ping pour 10.33.78.96:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 96ms, Maximum = 120ms, Moyenne = 106ms

#### un site internet

commande :

ping google.com

résultat :

Envoi d’une requête 'ping' sur google.com [142.250.178.142] avec 32 octets de données :

Réponse de 142.250.178.142 : octets=32 temps=16 ms TTL=117

Réponse de 142.250.178.142 : octets=32 temps=18 ms TTL=117

Réponse de 142.250.178.142 : octets=32 temps=15 ms TTL=117

Réponse de 142.250.178.142 : octets=32 temps=19 ms TTL=117

Statistiques Ping pour 142.250.178.142:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 15ms, Maximum = 19ms, Moyenne = 17ms

### 🌞 Faire une requête DNS à la main

commande : 

nslookup

résultat :

Serveur par dÚfaut :   dns.google
Address:  8.8.8.8

## III. Sniffer le réseau

### 🌞 J'attends dans le dépôt git de rendu un fichier ping.pcap

[Capture Wireshark](#ping.pcap)

### 🌞 Livrez un deuxième fichier : dns.pcap

[Capture Wireshark](#dns.pcap)

## IV. Network scanning et adresses IP

### 🌞 Effectue un scan du réseau auquel tu es connecté
commande :

 nmap.exe -sn -PR 10.33.64.0/20

résultat :

Starting Nmap 7.95 ( https://nmap.org ) at 2024-09-27 12:23 
Paris, Madrid (heure dÆÚtÚ)

Stats: 0:01:39 elapsed; 0 hosts completed (0 up), 4095 undergoing ARP Ping Scan

Nmap scan report for 10.33.66.78
Host is up (0.066s latency).
MAC Address: E4:B3:18:48:36:68 (Intel Corporate)

Nmap scan report for 10.33.67.113 
Host is up (0.048s latency). 
MAC Address: D2:91:DE:DF:9A:6E (Unknown)

### 🌞 Changer d'adresse IP

commande : 

netsh interface ipv4 set address name="Wi-Fi" static 10.33.66.79

résultat : 

Carte réseau sans fil Wi-Fi :
 
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.66.79(préféré)
 