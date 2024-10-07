# TP2 Réseaux

## I. Simplest LAN

### 1. Quelques pings

#### 🌞 Prouvez que votre configuration est effective
```
PS C:\Users\kevin\Downloads\netcat-win32-1.11\netcat-1.11> Get-NetIPAddress -InterfaceAlias Ethernet


IPAddress         : fe80::3f63:4219:fa87:e5c5%9
InterfaceIndex    : 9
InterfaceAlias    : Ethernet
AddressFamily     : IPv6
Type              : Unicast
PrefixLength      : 64
PrefixOrigin      : WellKnown
SuffixOrigin      : Link
AddressState      : Preferred
ValidLifetime     :
PreferredLifetime :
SkipAsSource      : False
PolicyStore       : ActiveStore

IPAddress         : 10.1.1.2
InterfaceIndex    : 9
InterfaceAlias    : Ethernet
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 24
PrefixOrigin      : Manual
SuffixOrigin      : Manual
AddressState      : Preferred
ValidLifetime     :
PreferredLifetime :
SkipAsSource      : False
PolicyStore       : ActiveStore
```
#### 🌞 Tester que votre LAN + votre adressage IP est fonctionnel
```
PS C:\Users\kevin\Downloads\netcat-win32-1.11\netcat-1.11> ping 10.1.1.3

Envoi d’une requête 'Ping'  10.1.1.3 avec 32 octets de données :
Réponse de 10.1.1.3 : octets=32 temps<1ms TTL=128
Réponse de 10.1.1.3 : octets=32 temps<1ms TTL=128
Réponse de 10.1.1.3 : octets=32 temps<1ms TTL=128
Réponse de 10.1.1.3 : octets=32 temps<1ms TTL=128

Statistiques Ping pour 10.1.1.3:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms
```
#### 🌞 Capture de ping

[Capture Wireshark](#ping.pcap)

## II. Utilisation des ports 

### 1. Animal numérique

#### 🌞 Sur le PC serveur
```
PS C:\Users\kevin\Downloads\netcat-win32-1.11\netcat-1.11> .\nc -lvp 8888
listening on [any] 8888 ...
DNS fwd/rev mismatch: Pt_William != Pt_William.local
connect to [10.1.1.2] from Pt_William [10.1.1.3] 39066
```
#### 🌞 Sur le PC serveur toujours
```
 Impossible d’obtenir les informations de propriétaire
  TCP    10.1.1.2:8888          10.1.1.3:39230         ESTABLISHED
```
#### 🌞 Sur le PC client
```
PS C:\Users\kevin\Downloads\netcat-win32-1.11\netcat-1.11> .\nc 10.1.1.2 8888
PS C:\Users\kevin\Downloads\netcat-win32-1.11\netcat-1.11>
```
#### 🌞 Echangez-vous des messages
```
PS C:\Users\kevin\Downloads\netcat-win32-1.11\netcat-1.11> .\nc -lvp 8888
listening on [any] 8888 ...
DNS fwd/rev mismatch: Pt_William != Pt_William.local
connect to [10.1.1.2] from Pt_William [10.1.1.3] 39066
cc
salut
ca va ?
```
#### 🌞 Utilisez une commande qui permet de voir la connexion en cours
```
PS C:\Users\kevin\Downloads\netcat-win32-1.11\netcat-1.11> netstat

Connexions actives

  Proto  Adresse locale         Adresse distante       État
  TCP    10.33.79.10:46851      20.199.120.182:https   ESTABLISHED
  TCP    10.33.79.10:47018      cdn-185-199-109-153:https  ESTABLISHED
  TCP    10.33.79.10:47029      a2-18-177-95:https     ESTABLISHED
  TCP    10.33.79.10:47133      a104-75-231-219:https  TIME_WAIT
  TCP    10.33.79.10:47136      a-0003:https           ESTABLISHED
  TCP    10.33.79.10:49283      20.199.120.182:https   ESTABLISHED
  TCP    10.33.79.10:50263      server-13-249-9-80:https  CLOSE_WAIT
  TCP    10.33.79.10:50323      server-13-249-9-80:https  CLOSE_WAIT
```
#### 🌞 Faites une capture Wireshark complète d'un échange

[Capture Wireshark](#netcat1.pcap)

#### 🌞 Inversez les rôles

message depuis le client 
```
PS C:\Users\kevin\Downloads\netcat-win32-1.11\netcat-1.11> ./nc64.exe 10.1.1.3 8888
salut man
ca va ?
```
netstat : 
```
PS C:\Users\kevin\Downloads\netcat-win32-1.11\netcat-1.11> netstat

Connexions actives

  Proto  Adresse locale         Adresse distante       État
  TCP    10.33.79.10:46851      20.199.120.182:https   ESTABLISHED
  TCP    10.33.79.10:47157      a2-18-40-144:https     CLOSE_WAIT
  TCP    10.33.79.10:47158      52.98.163.18:https     ESTABLISHED
```
netcat2

[Capture Wireshark](#netcat2.pcap)

## III. Analyse de vos applications usuelles

### 1. Serveur web

#### 🌞 Utilisez Wireshark pour capturer du trafic HTTP

[Capture Wireshark](#serveurweb.pcap)
### 2. Autres services

#### 🌞 Pour les 5 applications

netstat:
```
[Discord.exe]
  TCP    10.33.79.10:48732      162.159.130.234:443    ESTABLISHED
```
[Capture Wireshark](#service1.pcap)

netstat:
```
 [steam.exe]
  TCP    10.33.79.10:55356      20.199.120.182:443     ESTABLISHED
```
[Capture Wireshark](#service2.pcap)

netstat:
```
 [Spotify.exe]
  TCP    10.33.79.10:49417      142.250.178.138:443    ESTABLISHED
 [Spotify.exe]
  TCP    10.33.79.10:50263      13.249.9.80:443        CLOSE_WAIT
```
[Capture Wireshark](#service3.pcap)

netstat:
```
 [RiotClientServices.exe]
  TCP    10.33.79.10:49541      104.16.56.40:443       ESTABLISHED
 [RiotClientServices.exe]
  TCP    10.33.79.10:49544      104.18.41.183:443      ESTABLISHED
 [RiotClientServices.exe]
  TCP    10.33.79.10:49565      104.17.174.5:443       ESTABLISHED
 [RiotClientServices.exe]
  TCP    10.33.79.10:49605      172.65.252.238:5223    ESTABLISHED
 [RiotClientServices.exe]
  TCP    10.33.79.10:49608      104.18.41.183:443      ESTABLISHED
 [RiotClientServices.exe]
  TCP    10.33.79.10:49615      104.17.173.5:443       ESTABLISHED
 [RiotClientServices.exe]
  TCP    10.33.79.10:49616      104.17.173.5:443       ESTABLISHED
 [RiotClientServices.exe]
  TCP    10.33.79.10:49617      104.17.174.5:443       ESTABLISHED
 [RiotClientServices.exe]
  TCP    10.33.79.10:49626      172.64.147.231:443     ESTABLISHED
 [RiotClientServices.exe]
  TCP    10.33.79.10:49627      34.120.195.249:443     ESTABLISHED
 [Riot Client.exe]
  TCP    10.33.79.10:49636      35.186.224.26:443      TIME_WAIT
  TCP    10.33.79.10:49637      92.122.166.33:443      ESTABLISHED
 [Riot Client.exe]
  TCP    10.33.79.10:49638      92.122.166.33:443      ESTABLISHED
 [Riot Client.exe]
  TCP    10.33.79.10:49639      92.122.166.33:443      ESTABLISHED
 [Riot Client.exe]
  TCP    10.33.79.10:49640      92.122.166.33:443      ESTABLISHED
 [Riot Client.exe]
  TCP    10.33.79.10:49641      172.64.147.231:443     ESTABLISHED
 [Riot Client.exe]
  TCP    10.33.79.10:49642      104.17.173.5:443       ESTABLISHED
 [Riot Client.exe]
  TCP    10.33.79.10:49643      104.17.174.5:443       ESTABLISHED
 [Riot Client.exe]
  TCP    10.33.79.10:49644      104.17.173.5:443       ESTABLISHED
 [Riot Client.exe]
  TCP    10.33.79.10:49645      104.17.174.5:443       ESTABLISHED
 [Riot Client.exe]
  TCP    10.33.79.10:49646      104.17.173.5:443       ESTABLISHED
 [RiotClientServices.exe]
  TCP    10.33.79.10:49650      104.18.38.208:443      ESTABLISHED
 [Riot Client.exe]
  TCP    10.33.79.10:49651      104.19.146.81:443      ESTABLISHED
 [RiotClientServices.exe]
  TCP    10.33.79.10:49654      8.8.4.4:443            ESTABLISHED
 [Riot Client.exe]
  TCP    10.33.79.10:49656      162.159.61.3:443       ESTABLISHED
 [Riot Client.exe]
  TCP    10.33.79.10:49670      172.64.146.73:443      ESTABLISHED
```

[Capture Wireshark](#service4.pcap)

netstat:
```
 [TradingView.exe]
  TCP    192.168.1.1:26834      92.223.127.187:443     ESTABLISHED
 [TradingView.exe]
  TCP    192.168.1.1:26835      92.223.127.187:443     ESTABLISHED
 [TradingView.exe]
  TCP    192.168.1.1:26837      52.39.175.110:443      ESTABLISHED
 [TradingView.exe]
  TCP    192.168.1.1:26844      5.101.223.242:443      ESTABLISHED
 [TradingView.exe]
  TCP    192.168.1.1:26849      5.101.223.242:443      ESTABLISHED
 [TradingView.exe]
  TCP    192.168.1.1:50509      52.123.134.242:443     ESTABLISHED
```

[Capture Wireshark](#service5.pcap)