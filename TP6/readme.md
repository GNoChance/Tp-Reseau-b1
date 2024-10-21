# TP6 : Des bo services dans des bo LANs

> Dans ce TP, on construit encore et toujours sur ce qui a déjà été fait.

➜ **Ce qu'on garde des TPs précédents :**

- des VMs partout, et des réseaux privé-hôte (host-only) pour créer nos LANs
- le routeur qui permet à tout le monde de joindre internet
- un serveur DHCP qui propose tout ce qu'il faut aux clients (une IP libre, un DNS joignable, l'adresse de la passerelle)

➜ **Au menu de ce nouveau TP :**

- **deux LANs !**
  - un pour les clients
  - un pour nos serveurs
  - un peu comme le réseau de l'école
- **des services proposés au sein du LAN des serveurs**
  - un serveur DNS
  - un serveur Web


## Sommaire

- [TP6 : Des bo services dans des bo LANs](#tp6--des-bo-services-dans-des-bo-lans)
  - [Sommaire](#sommaire)
- [0. Prérequis](#0-prérequis)
- [I. Le setup](#i-le-setup)
  - [0. Schéma](#0-schéma)
  - [1. Tableau d'adressage](#1-tableau-dadressage)
  - [2. Marche à suivre](#2-marche-à-suivre)
  - [II. LAN clients](#ii-lan-clients)
  - [1. Serveur DHCP](#1-serveur-dhcp)
  - [2. Client](#2-client)
- [III. LAN serveurzzzz](#iii-lan-serveurzzzz)
  - [1. Serveur Web](#1-serveur-web)
  - [2. Serveur DNS](#2-serveur-dns)
  - [3. Serveur DHCP](#3-serveur-dhcp)

# 0. Prérequis

➜ **une machine Rocky Linux prête à être clonée**

- on s'en servira pour tout ce qui est routeur/serveur etc.
- descendez bien à 1CPU et 1Go de RAM pour économiser des ressources

➜ **votre machine Ubuntu prête à être clonée**

- pour le(s) client(s) toujours !
- idem, limitez les ressources (1CPU, 1Go de RAM, 128Mo mémoire vidéo vu que c'est avec interface graphique)

➜ **Ayez [les mémos](../../cours/memo/README.md) sous le coude !**

# I. Le setup

## 0. Schéma


> *J'ai inclus votre PC en transparence juste pour bien vous rappeler qu'il est là dans les réseaux, qu'il a aussi une interface branchée aux switches host-only, et que c'est pour cette raison qu'il peut ping tout le monde et vous permettre le SSH.*

## 1. Tableau d'adressage

| Machine          | LAN1 `10.6.1.0/24` | LAN2 `10.6.2.0/24` |
|------------------|--------------------|--------------------|
| `dhcp.tp6.b1`    | `10.6.1.253`       | x                  |
| `client1.tp6.b1` | DHCP               | x                  |
| `routeur.tp6.b1`  | `10.6.1.254`       | `10.6.2.254`       |
| `web.tp6.b1`     | x                  | `10.6.2.11`        |
| `dns.tp6.b1`     | x                  | `10.6.2.12`        |
| Votre PC         | `10.6.1.1`         | `10.6.2.1`         |

## 2. Marche à suivre

➜ **Créez deux nouveaux host-only**

- attribuez à votre PC les adresses IP indiquées dans le tableau d'adressage

➜ **Créez toutes les machines virtuelles**

- clones dans tous les sens
- Ubuntu (ou OS de votre choix) pour `client1.tp6.b1`
- Rocky Linux pour tous les autres
- branchez-les aux bons host-only :D

➜ **Allumez tout et attribuez les adresses IP indiquées**

- sauf `client1.tp6.b1`, no need pour le moment
- configuration réseau sur toutes les machines :
  - adresse IP statique sur les cartes host-only
  - indiquez l'adresse IP de votre routeur comme passerelle
    - laquelle ? Celle qui est dans ton LAN !
    - par exemple, une machine du LAN1, bah sa passerelle c'est `10.6.1.254` (et PAS `10.6.2.254`)
  - indiquez `1.1.1.1` comme DNS
- n'oubliez pas d'activer le routage sur `routeur.tp6.b1`
- **NOMMEZ VOS MACHINES** (configuration du `hostname`, voir mémo)

☀️ **Prouvez que...**

- une machine du LAN1 peut joindre internet (ping un nom de domaine)
- une machine du LAN2 peut joindre internet (ping nom de domaine)
- une machine du LAN1 peut joindre une machine du LAN2 (ping une adresse IP)

> *Prouvez que tout fonctionne quoi ! :d Je veux voir le nom des machines dans le prompt, vous **devez donc avoir nommé vos machines**.*

Ping dhcp :
```
[gno@dhcp ~]$ ping google.com
PING google.com (142.251.37.174) 56(84) bytes of data.
64 bytes from mrs09s14-in-f14.1e100.net (142.251.37.174): icmp_seq=1 ttl=111 time=17.8 ms
64 bytes from mrs09s14-in-f14.1e100.net (142.251.37.174): icmp_seq=2 ttl=111 time=15.6 ms
64 bytes from mrs09s14-in-f14.1e100.net (142.251.37.174): icmp_seq=3 ttl=111 time=15.8 ms
64 bytes from mrs09s14-in-f14.1e100.net (142.251.37.174): icmp_seq=4 ttl=111 time=16.8 ms
64 bytes from mrs09s14-in-f14.1e100.net (142.251.37.174): icmp_seq=5 ttl=111 time=15.9 ms
64 bytes from mrs09s14-in-f14.1e100.net (142.251.37.174): icmp_seq=6 ttl=111 time=16.0 ms
^C
--- google.com ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5008ms
rtt min/avg/max/mdev = 15.580/16.312/17.800/0.762 ms
```
Ping Dns :
```
[root@dns gno]# ping ynov.com
PING ynov.com (104.26.10.233) 56(84) bytes of data.
64 bytes from 104.26.10.233 (104.26.10.233): icmp_seq=1 ttl=53 time=15.8 ms
64 bytes from 104.26.10.233 (104.26.10.233): icmp_seq=2 ttl=53 time=16.3 ms
64 bytes from 104.26.10.233 (104.26.10.233): icmp_seq=3 ttl=53 time=18.0 ms
64 bytes from 104.26.10.233 (104.26.10.233): icmp_seq=4 ttl=53 time=15.4 ms
^C
--- ynov.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3006ms
rtt min/avg/max/mdev = 15.448/16.392/17.965/0.962 ms
```
Ping l'autre LAN:
```
[gno@dhcp ~]$ ping 10.6.2.254
PING 10.6.2.254 (10.6.2.254) 56(84) bytes of data.
64 bytes from 10.6.2.254: icmp_seq=1 ttl=64 time=0.489 ms
64 bytes from 10.6.2.254: icmp_seq=2 ttl=64 time=0.480 ms
64 bytes from 10.6.2.254: icmp_seq=3 ttl=64 time=0.454 ms
^C
--- 10.6.2.254 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2080ms
rtt min/avg/max/mdev = 0.454/0.474/0.489/0.014 ms
```

## II. LAN clients

Partie dédiée à la configuration du LAN1 : `10.6.1.0/24`. Un réseau dans lequel des clients pourraient se connecter, accéder à internet, et plus tard accéder à nos services internes (un ptit site web de fou, ce sera partie III).

## 1. Serveur DHCP

> *A faire sur `dhcp.tp6.b1`.*

Dans cette section, install + config du serveur DHCP. Pour que tous les clients du LAN1 puisse avoir un accès au réseau dès leur connexion !

➜ **Installez et configurez un serveur DHCP sur `dhcp.tp6.b1`**

- pareil qu'au TP5
- il doit attribuer des IPs entre `.37` et `.137`
- il doit indiquer la bonne passerelle pour les clients
- il doit indiquer `1.1.1.1` comme serveur DNS aux clients

## 2. Client

> *A faire sur `client1.tp6.b1`.*

➜ **Allumez `client1.tp6.b1` et configurez sa carte réseau en DHCP**

- il devrait récupérer automatiquement une adresse IP auprès de votre serveur DHCP
- et apprendre l'adresse de la passerelle de ce réseau
- et l'adresse d'un DNS utilisable

☀️ **Prouvez que...**

- le client a bien récupéré une adresse IP en DHCP
  - avec un `ip a` le mot-clé `dynamic` doit être écrit sur la ligne qui contient l'adresse IP
  - (y'a pas ce mot-clé quand tu définis une IP statique)
- vous avez bien `1.1.1.1` en DNS
  - commande dans le mémo pour consulter l'adresse IP du serveur DNS connu
- vous avez bien la bonne passerelle indiquée
  - idem, dans le mémo, pour afficher l'adresse de la passerelle actuellement configurée :)
- que ça `ping` un nom de domaine public sans problème magueule

```
kevin@client1:~/Desktop$ ping google.com
PING google.com (142.251.37.46) 56(84) bytes of data.
64 bytes from mrs09s13-in-f14.1e100.net (142.251.37.46): icmp_seq=1 ttl=112 time=18.5 ms
64 bytes from mrs09s13-in-f14.1e100.net (142.251.37.46): icmp_seq=2 ttl=112 time=23.0 ms
64 bytes from mrs09s13-in-f14.1e100.net (142.251.37.46): icmp_seq=3 ttl=112 time=17.5 ms
64 bytes from mrs09s13-in-f14.1e100.net (142.251.37.46): icmp_seq=4 ttl=112 time=37.2 ms
64 bytes from mrs09s13-in-f14.1e100.net (142.251.37.46): icmp_seq=5 ttl=112 time=20.5 ms
64 bytes from mrs09s13-in-f14.1e100.net (142.251.37.46): icmp_seq=6 ttl=112 time=19.4 ms
^C
--- google.com ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5004ms
rtt min/avg/max/mdev = 17.488/22.684/37.187/6.715 ms
kevin@client1:~/Desktop$ sudo cat /etc/netplan/01-netcfg.yaml 
[sudo] password for kevin: 
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s8:
      dhcp4: yes
```


# III. LAN serveurzzzz

Troisième partie, dédiée à la configuration de nos deux serveurs. Pour l'instant c'est juste des Rocky sans âme :d

## 1. Serveur Web

## 1. Intro serveur Web

On appelle *serveur Web* une machine qui exécute un programme qu'on appelle un *service Web*.

Un *service Web* est un programme qui écoute sur un port, attend la connexion de clients.

> *Par convention, le port sur lequel écoute les services Web HTTP c'est 80/tcp.*

Un client est supposé se connecter au port envoyer une requête HTTP, et le serveur renverra alors une page HTML qui correspond à la requête du client.

> Comme `nc` aux premiers TPs, sauf que quand tu te connectes, et que t'envoies la bonne requête, bah le service Web répond automatiquement une page HTML.

On va utiliser un service Web qui s'appelle NGINX, réputé pour ses performances et sa stabilité !

## 2. Install this shiet

> *On est sur notre (futur) beau serveur Web `web.tp6.b1`.*

On est pas en cours de Linux, ni en cours de dév Web, alors on va s'intéresser à la partie réseau uniquement, et limiter au maximum les interactions spécifiques à Linux.

➜ **Installez et démarrez le service web NGINX**

```bash
# installation du service NGINX
sudo dnf install -y nginx

# démarrage du service NGINX
sudo systemctl enable --now nginx
```

Devrait po y avoir d'erreurs, et ça tourne !

> Le service web NGINX propose une page d'accueil (toute moche) par défaut. On aura même pas besoin d'écrire une vieille page HTML pour tester :D Il permet si on le configure d'héberger les sites Web de notre choix dans une utilisation réelle.

### 3. Analyse et test

☀️ **Déterminer sur quel port écoute le serveur NGINX**

- commande dans le mémooooooo
- isolez la ligne intéressante avec un `... | grep <PORT>` une fois que vous avez repéré le port

> *C'est le port conventionnel ! Pour un service Web, qui reçoit donc des requêtes HTTP, c'est ce port qui est la convention :) C'est aussi écrit dans la configuration quelque port qu'il doit écouter sur ce port. Conf dans le dossier `/etc/nginx/` pour les curieux.*

```
[root@web gno]# ss -lnpt | grep 80
LISTEN 0      511          0.0.0.0:80        0.0.0.0:*    users:(("nginx",pid=1809,fd=6),("nginx",pid=1808,fd=6))
LISTEN 0      511             [::]:80           [::]:*    users:(("nginx",pid=1809,fd=7),("nginx",pid=1808,fd=7))
```

☀️ **Ouvrir ce port dans le firewall**

- bien que NGINX soit en train d'écouter sur le port repéré, et attende la connexion de clients potentiels...
- ... ça empêche pas le firewall de bloquer les clients si on ouvre pas le port
- commande dans le mémooooo pour faire ça aussi

```
[root@web gno]# firewall-cmd --permanent --add-port=80/tcp
success
[root@web gno]# firewall-cmd --reload
success
```

☀️ **Visitez le site web !**

- depuis `client1.tp6.b1`, ouvrez un navigateur, et visitez `http://10.6.2.11` (l'adresse IP du serveur web `web.tp6.b1`)
- normalement, page d'accueil !
  - sinon, tu t'es planté, vérifie tout !
- pour le compte-rendu, depuis le terminal de `client1.tp6.b1` : `curl http://10.6.2.11`
  - vous me mettez que les premières lignes du résultat SVP :D
  - la commande `curl` envoie une requête HTTP (comme un navigateur), en réponse on tout l'HTML de la page... bonne bouillie :D

```
kevin@client1:~/Desktop$ sudo curl http://10.6.2.11
<!doctype html>
<html>
  <head>
    <meta charset='utf-8'>
    <meta name='viewport' content='width=device-width, initial-scale=1'>
    <title>HTTP Server Test Page powered by: Rocky Linux</title>
    <style type="text/css">
      /*<![CDATA[*/
```

## 2. Serveur DNS

## 1. Présentation serveur DNS

➜ **Un *serveur DNS* est une machine sur laquelle s'exécute un programme qu'on appelle *service DNS*.**

Un *service DNS* écoute sur un port, et attend la connexion de clients.

> Par convention, les services DNS écoutent sur les ports 53/tcp et 53/udp.

➜ **Un *service DNS* attend qu'un client se connecte et lui envoie une *requête DNS*.**

Une *requête DNS* est la requête effectuée par une machine lorsqu'elle souhaite connaître l'adresse IP qui correspond à un nom de domaine.

> Par exemple, si vous ouvrez un navigateur web et saisissez `https://www.ynov.com` alors une *requête DNS* est automatiquement et spontanément effectuée par votre PC pour déterminez à quelle adresse IP correspond le nom `www.ynov.com`.

➜ **Une fois que vous connaissez l'adresse IP, vous pouvez vous connecter à cette adresse IP.**

> *La partie `https://` ne fait pas partie du nom de domaine, ça indique simplement au navigateur la méthode de connexion. Ici, c'est HTTPS.*

## 2. Dans notre TP

> *Dans cette partie, on va installer et configurer notre propre serveur DNS. Le service qu'on va installer s'appelle BIND9. C'est genre le service DNS le plus utilisé au monde. Rien k'sa.*

Notre serveur DNS répondra aux autres VMs du LAN (comme le `client1.tp6.b1`) quand elles auront besoin de connaître des noms. Ainsi, ce serveur pourra :

- **résoudre des noms locaux**
  - vous pourrez `ping web.tp6.b1` et ça fonctionnera
  - même visiter le site web avec `http://web.tp6.b1` dans un navigateur
- **mais aussi résoudre des noms publics**
  - genre `ping www.ynov.com` et votre serveur DNS sera capable de le résoudre aussi

> En réalité, ce n'est pas votre serveur DNS qui pourra résoudre `www.ynov.com`, mais il sera capable de *forward* (faire passer) votre requête à un autre serveur DNS qui lui, connaît la réponse. Comme `1.1.1.1` (serveur DNS de CloudFlare) ou `8.8.8.8` (serveur DNS de Google).

Une fois qu'il sera installé et configuré, on pourra indiquer aux clients de l'utiliser comme serveur DNS.

## 3. Zé bardi

> *Pour cette partie, on est sur le beau `dns.tp6.b1`.*

Sur internet ça peut partir dans tous les sens, alors je vous ai écrit un petit tuto maison cette fois hehe. **Lisez attentivement toute cette partie en entier avant de commencer les manips.** Pour mieux capter ce que vous faites. Ha et dernière chose : good luck, parce que la syntaxe de configuration de BIND, elle fait mal aux dents.
 

Installation du serveur DNS :

```bash
# installation du serveur DNS, son p'tit nom c'est BIND9
$ sudo dnf install -y bind bind-utils
```

La configuration du serveur DNS va se faire dans 3 fichiers essentiellement :

- **un fichier de configuration principal**
  - `/etc/named.conf`
  - on définit les trucs généraux, comme les adresses IP et le port où on veu écouter
  - on définit aussi un chemin vers les autres fichiers, les fichiers de zone
- **un fichier de zone**
  - `/var/named/tp6.b1.db`
  - je vous préviens, la syntaxe fait mal
  - on peut y définir des correspondances `nom ---> IP`
- **un fichier de zone inverse**
  - `/var/named/tp6.b1.rev`
  - on peut y définir des correspondances `IP ---> nom`

➜ **Allooooons-y, fichier de conf principal**

- je ne vous mets que les lignes les plus importantes, et celles qu'on modifie
- les `[...]` indiquent qu'il faut laisser la conf par défaut, que j'ai enlevé ici pour que ce soit + lisible
  - il ne faut donc pas les mettre dans vos fichiers

```bash
# éditez le fichier de config principal pour qu'il ressemble à :
$ sudo cat /etc/named.conf
options {
        listen-on port 53 { 127.0.0.1; any; };
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
[...]
        allow-query     { localhost; any; };
        allow-query-cache { localhost; any; };

        recursion yes;
[...]
# référence vers notre fichier de zone
zone "tp6.b1" IN {
     type master;
     file "tp6.b1.db";
     allow-update { none; };
     allow-query {any; };
};
# référence vers notre fichier de zone inverse
zone "2.6.10.in-addr.arpa" IN {
     type master;
     file "tp6.b1.rev";
     allow-update { none; };
     allow-query { any; };
};
```

➜ **Et pour les fichiers de zone**

- dans ces fichiers c'est le caractère `;` pour les commentaires
- hésitez pas à virer mes commentaires de façon générale
- c'juste pour que vous captiez mais ça fait dégueu dans un fichier de conf

```bash
# Fichier de zone pour nom -> IP

$ sudo cat /var/named/tp6.b1.db

$TTL 86400
@ IN SOA dns.tp6.b1. admin.tp6.b1. (
    2019061800 ;Serial
    3600 ;Refresh
    1800 ;Retry
    604800 ;Expire
    86400 ;Minimum TTL
)

; Infos sur le serveur DNS lui même (NS = NameServer)
@ IN NS dns.tp6.b1.

; Enregistrements DNS pour faire correspondre des noms à des IPs
web       IN A 10.6.2.11
dns       IN A 10.6.2.12
```

```bash
# Fichier de zone inverse pour IP -> nom

$ sudo cat /var/named/tp6.b1.rev

$TTL 86400
@ IN SOA dns.tp6.b1. admin.tp6.b1. (
    2019061800 ;Serial
    3600 ;Refresh
    1800 ;Retry
    604800 ;Expire
    86400 ;Minimum TTL
)

; Infos sur le serveur DNS lui même (NS = NameServer)
@ IN NS dns.tp6.b1.

; Reverse lookup
11 IN PTR web.tp6.b1.
12 IN PTR dns.tp6.b1.
```

➜ **Une fois ces 3 fichiers en place, démarrez le service DNS**

```bash
# Démarrer le service
$ sudo systemctl enable --now named
# OUI JE SAIS le truc s'appelle bind9 et faut démarrer "named"
# et moi, je vous en pose des questions ?

# Obtenir des infos sur le service
$ sudo systemctl status named

# Obtenir des logs en cas de probème
$ sudo journalctl -xe -u named
```

## 4. Analyse du service

☀️ **Déterminer sur quel(s) port(s) écoute le service BIND9**

- isolez la ligne intéressante avec un `... | grep <PORT>` une fois que vous avez repéré le port
- bon normalement vous l'avez vu dans la conf, non ? :d



☀️ **Ouvrir ce(s) port(s) dans le firewall**

## 5. Tests manuels

☀️ **Effectuez des requêtes DNS manuellement** depuis le serveur DNS lui-même dans un premier temps

```bash
# dig permet de faire des requêtes DNS manuellement sous Linux
# cette commande demande à 10.6.2.12 à quelle IP correspond web.tp6.b1
dig web.tp6.b1 @10.6.2.12

# lui doit fonctionner aussi
dig dns.tp6.b1 @10.6.2.12

# et ça aussi dukou !
dig ynov.com @10.6.2.12

# et on devrait aussi pouvoir faire l'inverse en ajoutant -x :
# demander quel est le nom qui correspond à une IP donnée
dig -x 10.6.2.11 @10.6.2.12
dig -x 10.6.2.12 @10.6.2.12
```

☀️ **Effectuez une requête DNS manuellement** depuis `client1.tp6.b1`

- pour obtenir l'adresse IP qui correspond au nom `web.tp6.b1`

☀️ **Capturez une requête DNS et la réponse de votre serveur**

- vous pouvez télécharger Wireshark sur `client1.tp6.b1` mais bon... bourrin
- sinon vous pouvez lancer une capture réseau avec `tcpdump`, on pourra ensuite ouvrir le fichier avec Wireshark (sur votre PC, pas dans une VM) pour visualiser

```bash
# principe de la commande
tcpdump -w <NOM_DU_FICHIER> -i <INTERFACE_A_CAPTURER>

# par exemple, si vous voulez enregistrer tout ce qui passe par enp0s3
# et enregistrer ça dans un fichier toto.pcap
tcpdump -w toto.pcap -i enp0s3
```


## 3. Serveur DHCP

What ?! Again ?!

Nan nan juste, on va aller changer une ligne dans la configuration de `dhcp.tp6.b1`.

Bah oui ! On a notre propre serveur DNS maintenant ! Faut le dire à nos clients qu'ils peuvent l'utiliser :d

➜ **Editez la configuration du serveur DHCP sur `dhcp.tp6.b1`**

- faut qu'il file l'adresse IP `10.6.2.12` comme DNS à tous les nouveaux clients !

☀️ **Créez un nouveau client `client2.tp6.b1` vitefé**

- supprimez le `client1` si vous voulez, on a pu besoin
- récupérez une IP en DHCP sur ce nouveau `client2.tp6.b1`
- vérifiez que vous avez bien `10.6.2.12` comme serveur DNS à contacter
  - commande dans le mémo pour voir le serveur DNS connu actuellement

➜ **Vous devriez pouvoir visiter `http://web.tp6.b1` avec le navigateur, ça devrait fonctionner sans aucune autre action.**

