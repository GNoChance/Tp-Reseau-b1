# TP5 : Un ptit LAN à nous

Premier TP avec des machines virtuelles ! C'est ce qu'on va avoir de mieux pour connecter plusieurs machines dans un réseau local (LAN en anglais), en maîtrisant tout ce qu'il se passe dans ce LAN.

**Ce qu'il y a de plus pratique pour tester des machins !** Habituez-vous :d

> *On ne sera donc pas tributaire d'un équipement un peu "boîte noire" comme dans les TPs précédents : on dépendait toujours de votre partage de co, du routeur de YNOV ou de chez vous, etc.*

![Router](./img/router.jpg)

## Sommaire

- [TP5 : Un ptit LAN à nous](#tp5--un-ptit-lan-à-nous)
  - [Sommaire](#sommaire)
- [I. Setup](#i-setup)
- [II. Accès internet pour tous](#ii-accès-internet-pour-tous)
  - [1. Accès internet routeur](#1-accès-internet-routeur)
  - [2. Accès internet clients](#2-accès-internet-clients)
- [III. Serveur SSH](#iii-serveur-ssh)
- [IV. Serveur DHCP](#iv-serveur-dhcp)
  - [1. Le but](#1-le-but)
  - [2. Comment le faire](#2-comment-le-faire)
  - [3. Rendu attendu](#3-rendu-attendu)
    - [A. Installation et configuration du serveur DHCP](#a-installation-et-configuration-du-serveur-dhcp)
    - [B. Test avec un nouveau client](#b-test-avec-un-nouveau-client)
    - [C. Consulter le bail DHCP](#c-consulter-le-bail-dhcp)


# I. Setup

➜ **Ptit tableau avec les adresses IP**

| Nom de la machine | IP dans le LAN `10.5.1.0/24` |
|-------------------|------------------------------|
| Votre PC          | `10.5.1.1`                   |
| `client1.tp5.b1`  | `10.5.1.11`                  |
| `client2.tp5.b1`  | `10.5.1.12`                  |
| `routeur.tp5.b1`  | `10.5.1.254`                 |

> `/24` correspond à un masque de `255.255.255.0`. C'est deux façons différentes d'écrire la même chose, on voit ça bientôt. Il faudra indiquer le même masque à toutes les machines.


> *Pour rappel la carte host-only, comme montrée sur le schéma, permet que tout le monde soit connecté à un switch virtuel, et ainsi former un LAN (réseau local). La carte NAT permet uniquement un accès internet.*

➜ **Ensuite, pour chaque VM...**

- **configurer l'adresse IP demandée**
  - ça se fait depuis la VM directement : chaque client choisit sa propre IP comme toujours !
  - mettez l'IP indiquée dans le tableau, et le même masque pour tout le monde
- **configurer un *hostname* pour la VM**
  - comme ça, quand on est dans un terminal, le nom de la machin est affiché, et on sait où on est !
  - c'est affiché dans le prompt dans votre terminal : `[it4@localhost]$`
  - le nom par défaut c'est `localhost` et c'est pourri !

➜ **Il faut donc, sur votre PC :**

- créer un host-only (réseau privé-hôte en français)
- définir l'IP `10.5.1.1/24` pour le PC depuis l'interface de VBox
- ajouter les cartes réseau nécessaires à toutes les VMs
  - 1 carte host-only pour les 3 VMs (clients et routeur)
  - 1 carte NAT en + pour le routeur

☀️ **Uniquement avec des commandes, prouvez-que :**

Rocky:


```
[gno@localhost ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:1b:93:59 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute enp0s3
       valid_lft 86290sec preferred_lft 86290sec
    inet6 fe80::a00:27ff:fe1b:9359/64 scope link
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:96:1f:e4 brd ff:ff:ff:ff:ff:ff
    inet 10.5.1.254/24 brd 10.5.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe96:1fe4/64 scope link
       valid_lft forever preferred_lft forever
 ```
 hostname :
 ```
 [gno@localhost ~]$ sudo hostnamectl set-hostname routeur.tp5.b1
[gno@localhost ~]$ sudo hostnamectl
 Static hostname: routeur.tp5.b1
       Icon name: computer-vm
         Chassis: vm 🖴
      Machine ID: 7130b546f0e9485b86ecab0ba3049111
         Boot ID: 0b6f242f0d3349ffb8122d4d1c7ef87e
  Virtualization: oracle
Operating System: Rocky Linux 9.4 (Blue Onyx)
     CPE OS Name: cpe:/o:rocky:rocky:9::baseos
          Kernel: Linux 5.14.0-427.13.1.el9_4.x86_64
    Architecture: x86-64
 Hardware Vendor: innotek GmbH
  Hardware Model: VirtualBox
Firmware Version: VirtualBox
 ```
 ```
[gno@routeur ~]$
```

 ```
[gno@routeur ~]$ ping 10.5.1.11
PING 10.5.1.11 (10.5.1.11) 56(84) bytes of data.
64 bytes from 10.5.1.11: icmp_seq=1 ttl=64 time=0.421 ms
64 bytes from 10.5.1.11: icmp_seq=2 ttl=64 time=0.535 ms
64 bytes from 10.5.1.11: icmp_seq=3 ttl=64 time=0.378 ms
64 bytes from 10.5.1.11: icmp_seq=4 ttl=64 time=0.411 ms
^C
--- 10.5.1.11 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3105ms
rtt min/avg/max/mdev = 0.378/0.436/0.535/0.059 ms
 ``` 

 ubuntu client 1 :

 ```
 kevin@kevin-ubuntu:~/Desktop$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:7f:9d:86 brd ff:ff:ff:ff:ff:ff
    inet 10.5.1.11/24 brd 10.5.1.255 scope global enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe7f:9d86/64 scope link 
       valid_lft forever preferred_lft forever
```

hostname:
```
kevin@kevin-ubuntu:~/Desktop$ sudo hostnamectl set-hostname client1.tp5.b1
[sudo] password for kevin: 
kevin@kevin-ubuntu:~/Desktop$ sudo hostnamectl 
 Static hostname: client1.tp5.b1
       Icon name: computer-vm
         Chassis: vm 🖴
      Machine ID: 870e24db80ae40f6af108dca5fc03580
         Boot ID: 581885257c5b49878674453c613a92f1
  Virtualization: oracle
Operating System: Ubuntu 24.04.1 LTS              
          Kernel: Linux 6.8.0-45-generic
    Architecture: x86-64
 Hardware Vendor: innotek GmbH
  Hardware Model: VirtualBox
Firmware Version: VirtualBox
   Firmware Date: Fri 2006-12-01
    Firmware Age: 17y 10month 2w 1d 
```
```
kevin@client1:~/Desktop$ 
```
```
kevin@client1:~/Desktop$ ping 10.5.1.12
PING 10.5.1.12 (10.5.1.12) 56(84) bytes of data.
64 bytes from 10.5.1.12: icmp_seq=1 ttl=64 time=0.512 ms
64 bytes from 10.5.1.12: icmp_seq=2 ttl=64 time=0.451 ms
64 bytes from 10.5.1.12: icmp_seq=3 ttl=64 time=0.428 ms
^C
--- 10.5.1.12 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 0.428/0.463/0.512/0.035 ms
```

ubuntu client 2 :
```
kevin@kevin-ubuntu:~/Desktop$ ip a 
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:db:3f:f9 brd ff:ff:ff:ff:ff:ff
    inet 10.5.1.12/24 brd 10.5.1.255 scope global enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fedb:3ff9/64 scope link 
       valid_lft forever preferred_lft forever
```
hostname :

```
kevin@kevin-ubuntu:~/Desktop$ sudo hostnamectl set-hostname client2.tp5.b1
[sudo] password for kevin: 
kevin@kevin-ubuntu:~/Desktop$ sudo hostnamectl
 Static hostname: client2.tp5.b1
       Icon name: computer-vm
         Chassis: vm 🖴
      Machine ID: 870e24db80ae40f6af108dca5fc03580
         Boot ID: 696a849e0954495bbd907cf7da2ba6b0
  Virtualization: oracle
Operating System: Ubuntu 24.04.1 LTS              
          Kernel: Linux 6.8.0-45-generic
    Architecture: x86-64
 Hardware Vendor: innotek GmbH
  Hardware Model: VirtualBox
Firmware Version: VirtualBox
   Firmware Date: Fri 2006-12-01
    Firmware Age: 17y 10month 2w 1d 
```
```
kevin@client2:~/Desktop$ 
```
```
kevin@client2:~/Desktop$ ping 10.5.1.254
PING 10.5.1.254 (10.5.1.254) 56(84) bytes of data.
64 bytes from 10.5.1.254: icmp_seq=1 ttl=64 time=0.430 ms
64 bytes from 10.5.1.254: icmp_seq=2 ttl=64 time=0.459 ms
64 bytes from 10.5.1.254: icmp_seq=3 ttl=64 time=0.445 ms
64 bytes from 10.5.1.254: icmp_seq=4 ttl=64 time=0.433 ms
^C
--- 10.5.1.254 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3076ms
rtt min/avg/max/mdev = 0.430/0.441/0.459/0.011 ms
```
- vous avez bien configuré les adresses IP demandées (un `ip a` suffit hein)
- vous avez bien configuré les *hostnames* demandés
- tout le monde peut se ping au sein du réseau `10.5.1.0/24`

> *Dès que votre PC peut `ping` tout le monde, je vous conseille **fortement** de vous connecter en SSH à vos VMs. En particulier la VM Rocky qui a déjà un serveur SSH qui tourne par défaut.*

# II. Accès internet pour tous

➜ **Actuellement, tout le monde est connecté, mais les clients n'ont pas internet !**

➜ Dans cette partie, on va faire en sorte que tout le monde ait un accès internet :

- le routeur a déjà un accès internet
- les clients vont se servir du routeur comme passerelle afin d'accéder à internet

Pour ça :


## 1. Accès internet routeur

> *Cette section 1. est à réaliser sur `routeur.tp5.b1`.*

☀️ **Déjà, prouvez que le routeur a un accès internet**

- une seule commande `ping` suffit à prouver ça, vers un nom de domaine que vous connaissez, genre `www.ynov.com` (ou autre de votre choix :d)

> Tout est normalement déjà setup avec la carte NAT ! Si vous n'avez pas internet, c'est que votre carte NAT est éteinte. Allumez-la !

```
[gno@routeur ~]$ ping www.ynov.com
PING www.ynov.com (172.67.74.226) 56(84) bytes of data.
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=1 ttl=55 time=15.5 ms
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=2 ttl=55 time=13.9 ms
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=3 ttl=55 time=13.7 ms
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=4 ttl=55 time=13.0 ms
^C
--- www.ynov.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 13.038/14.050/15.515/0.907 ms
```
☀️ **Activez le routage**

- toujours sur `routeur.tp5.b1`
- la commande est dans le mémo toujours !
```
sudo: firewall: command not found
[gno@routeur ~]$ sudo firewall-cmd --add-masquerade --permanent
Warning: ALREADY_ENABLED: masquerade
success
[gno@routeur ~]$ sudo firewall-cmd --reload
success
```

## 2. Accès internet clients

> *Cette section 2. est à réaliser sur `client1.tp5.b1` et `client2.tp5.b1`. Tout est dans [le mémo réseau Ubuntu](../../cours/memo/ubuntu.md).*

➜ **Définir l'adresse IP du routeur comme passerelle pour les clients**

- il sera peut-être nécessaire de redémarrer l'interface réseau pour que ça prenne effet

➜ **Vérifier que les clients ont un accès internet**

- avec un `ping` vers une adresse IP publique vous connaissez
- à ce stade, vos clients ne peuvent toujours pas résoudre des noms, donc impossible de visiter un site comme `www.ynov.com`

➜ **Définir `1.1.1.1` comme serveur DNS que peuvent utiliser les clients**

- redémarrez l'interface réseau si nécessaire pour que ça prenne effet
- ainsi vos clients pourront spontanément envoyer des requêtes DNS vers `1.1.1.1` afin d'apprendre à quelle IP correspond un nom de domaine donné

> *`1.1.1.1` c'est l'adresse IP publique d'un serveur DNS d'une entreprise qui s'appelle CloudFlare (un gros acteur du Web). Ils hébergent gracieusement et publiquement ce serveur DNS, afin que n'importe qui puisse l'utiliser.*

☀️ **Prouvez que les clients ont un accès internet**

- avec de la résolution de noms cette fois
- une seule commande `ping` suffit

```
kevin@client1:~/Desktop$ ping www.google.com
PING www.google.com (142.250.75.228) 56(84) bytes of data.
64 bytes from par10s41-in-f4.1e100.net (142.250.75.228): icmp_seq=1 ttl=113 time=13.6 ms
64 bytes from par10s41-in-f4.1e100.net (142.250.75.228): icmp_seq=2 ttl=113 time=13.2 ms
64 bytes from par10s41-in-f4.1e100.net (142.250.75.228): icmp_seq=3 ttl=113 time=14.1 ms
64 bytes from par10s41-in-f4.1e100.net (142.250.75.228): icmp_seq=4 ttl=113 time=13.1 ms
^C
--- www.google.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3004ms
rtt min/avg/max/mdev = 13.101/13.507/14.123/0.399 ms
```

☀️ **Montrez-moi le contenu final du fichier de configuration de l'interface réseau**

- celui de `client2.tp5.b1` me suffira
- pour le compte-rendu, une simple commande `cat` pour afficher le contenu du fichier

> *Vous devriez pouvoir ouvrir un navigateur et visiter des sites sans soucis sur les clients.*

```
kevin@client2:~/Desktop$ sudo cat /etc/netplan/01-netcfg.yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s8:
      dhcp4: yes
      addresses: [10.5.1.12/24]
      gateway4: 10.5.1.254
      nameservers:
        addresses: [1.1.1.1]
```

# III. Serveur SSH

> *Cette partie III. est à réaliser sur `routeur.tp5.b1`. Tout est dans [le mémo réseau Rocky](../../cours/memo/rocky.md).*

☀️ **Sur `routeur.tp5.b1`, déterminer sur quel port écoute le serveur SSH**

- pour le serveur SSH, le nom du programme c'est `sshd`
  - il écoute sur un port TCP
- dans le compte rendu je veux que vous utilisiez une syntaxe avec `... | grep <PORT>` pour isoler la ligne avec le port intéressant
  - par exemple si, vous repérez le port 8888, vous ajoutez ` | grep 8888` à votre commande, pour me mettre en évidence le por que vous avez repéré

```
[gno@routeur ~]$ sudo ss -lnpt | grep 22
LISTEN 0      128          0.0.0.0:22        0.0.0.0:*    users:(("sshd",pid=703,fd=3))
LISTEN 0      128             [::]:22           [::]:*    users:(("sshd",pid=703,fd=4))
```

☀️ **Sur `routeur.tp5.b1`, vérifier que ce port est bien ouvert**

- la commande est dans [le mémooooo](../../cours/memo/rocky.md) pour voir la configuration du pare-feu

> ***Si vous voyez le "service" `ssh` ouvert dans le pare-feu**, il correspond à un port bien précis. Pour voir la correspondance entre les "services" et le port associé, vous pouvez consulter le contenu du fichier `/etc/services`. Ca devrait correspondre à ce que vous avez vu juste avant !*

➜ Dernière fois que je le dis : **connectez-vous en SSH pour administrer la machine Rocky Linux**, n'utilisez pas l'interface console de VirtualBox.

```
[gno@routeur ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: ssh
  ports:
  protocols:
  forward: yes
  masquerade: yes
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
  ```

# IV. Serveur DHCP

## 1. Le but

➜ On installe et configure **notre propre serveur DHCP** dans cette partie ! Le but est le suivant :

- **dès qu'un client se connecte à notre réseau, il a automatiquement internet !**
- ça **évite de faire à la main** comme vous avez fait dans ce TP :
  - choisir et configurer une adresse IP
  - choisir et configurer l'adresse d'un serveur DNS
  - configurer l'adresse de la passerelle
- **dès qu'il se connecte, il essaiera automatiquement de contacter un serveur DHCP**
- **notre serveur DHCP lui proposera alors automatiquement tout le nécessaire pour avoir un accès internet**, à savoir :
  - une adresse IP disponible
  - l'adresse d'un serveur DNS
  - l'adresse de la passerelle du réseau


## 2. Comment le faire

> Cette fois, je vous ré-écris pas tout, je vous laisse chercher sur internet par vous-mêmes "install dhcp server rocky 9", ou vous référer [par exemple à ce lien](https://www.server-world.info/en/note?os=Rocky_Linux_8&p=dhcp&f=1) qui résume très bien la chose.

➜ Peu importe le lien que vous suivez, **les étapes seront les suivantes** :

- installation du paquet qui contient le serveur DHCP
  - commande `dnf install`
- modification de la configuration
  - c'est un fichier texte (comme toujours)
  - donc avec `nano` ou `vim` par exemple
- (re)démarrage du service DHCP
  - avec un `systemctl start`

➜ **Et si ça fonctionne pas, c'est que tu t'es planté dans le fichier de conf, donc tu vas lire pourquoi dans les logs** :

- voir les logs d'erreur
  - avec une commande `journalctl`
  - généralement, il dit clairement l'erreur
- ajustement de la configuration
  - c'est un fichier texte (comme toujours)
  - donc avec `nano` ou `vim` par exemple
- redémarrage du service DHCP
  - avec un `systemctl restart`

> N'hésitez pas, comme d'hab, à m'appeler si vous galérez avec cette section !

## 3. Rendu attendu

> *Vous pouvez éteindre `client1.tp5.b1` et `client2.tp5.b1` pour limiter l'utilisation des ressources hein.*

⚠️⚠️⚠️ **Vous n'avez le droit d'utiliser QUE des lignes que vous comprenez dans le fichier de configuration.** Et pour lesquelles vous avez adapté les valeurs au TP. **Vous devez enlever les lignes de configuration inutiles pour notre TP.**

### A. Installation et configuration du serveur DHCP

> *Cette section A. est à réaliser sur `routeur.tp5.b1`.*

☀️ **Installez et configurez un serveur DHCP sur la machine `routeur.tp5.b1`**

- je veux toutes les commandes réalisées
- et le contenu du fichier de configuration
- le fichier de configuration doit :
  - indiquer qu'on propose aux clients des adresses IP entre `10.5.1.137` et `10.5.1.237`
  - indiquer aux clients que la passerelle dans le réseau ici c'est `10.5.1.254`
  - indiquer aux clients qu'un serveur DNS joignable depuis le réseau c'est `1.1.1.1`

```
[gno@routeur ~]$ sudo dnf -y install dhcp-server
```
```
[root@routeur gno]# nano /etc/dhcp/dhcpd.conf
```
```
[root@routeur gno]# [root@routeur gno]# cat /etc/dhcp/dhcpd.conf
#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp-server/dhcpd.conf.example
#   see dhcpd.conf(5) man page

subnet 10.5.1.0 netmask 255.255.255.0 {
    range dynamic-bootp 10.5.1.137 10.5.1.237;
    option broadcast-address 10.5.1.254;
    option routers 10.5.1.1;
    option domain-name-servers 1.1.1.1;
}
```
```
[root@routeur gno]# systemctl enable --now dhcpdhcp/dhcpd.conf
```

### B. Test avec un nouveau client

> *Cette section B. est à réaliser sur une nouvelle machine Ubuntu fraîchement clonée : `client3.tp5.b1`. Vous pouvez éteindre `client1.tp5.b1` et `client2.tp5.b1` pour économiser des ressources.*

☀️ **Créez une nouvelle machine client `client3.tp5.b1`**

- définissez son *hostname*
- définissez une IP en DHCP
- vérifiez que c'est bien une adresse IP entre `.137` et `.237`
- prouvez qu'il a immédiatement un accès internet

```
root@client3:/home/kevin/Desktop# cat /etc/netplan/01-netcfg.yaml 
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s8:
      dhcp4: yes
```
```
root@client3:/home/kevin/Desktop# ping ynov.com
PING ynov.com (172.67.74.226) 56(84) bytes of data.
64 bytes from 172.67.74.226: icmp_seq=1 ttl=53 time=15.1 ms
64 bytes from 172.67.74.226: icmp_seq=2 ttl=53 time=15.3 ms
^C
--- ynov.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 15.126/15.215/15.304/0.089 ms
```

### C. Consulter le bail DHCP

➜ **Côté serveur DHCP, à chaque fois qu'une adresse IP est proposée à quelqu'un, le serveur crée un fichier texte appelé *bail DHCP*** (ou *DHCP lease* en anglais).

Il contient toutes les informations liées à l'échange avec le client, notamment :

- adresse MAC du client qui a demandé l'IP
- adresse IP proposée au client
- heure et date précises de l'échange DHCP
- durée de validité du *bail DHCP*

> *A l'issue de cette durée de validité, le client devra de nouveau contacter le serveur, pour s'assurer que l'adresse IP est toujours libre. Rappelez-vous que DHCP est utilisé partout pour attribuer automatiquement des adresses IP aux clients, à l'école, chez vous, etc. C'est nécessaire que le bail expire pour pas qu'un client qui se connecte une seule fois monopolise à vie une adresse IP.*

☀️ **Consultez le *bail DHCP* qui a été créé pour notre client**

- à faire sur `routeur.tp5.b1`
- toutes les données du serveur DHCP, comme les *baux DHCP*, sont stockés dans le dossier `/var/lib/dhcpd/`
- afficher le contenu du fichier qui contient les *baux DHCP*
- on devrait y voir l'IP qui a été proposée au client, ainsi que son adresse MAC
```
[root@routeur gno]# cat /var/lib/dhcpd/dhcpd.leases
lease 10.5.1.201 {
  starts 3 2024/10/16 09:36:04;
  ends 3 2024/10/16 21:36:04;
  cltt 3 2024/10/16 09:36:04;
  binding state active;
  next binding state free;
  rewind binding state free;
  hardware ethernet 08:00:27:17:45:6a;
  client-hostname "client3.tp5.b1";
}
```
☀️ **Confirmez qu'il s'agit bien de la bonne adresse MAC**

- à faire sur `client3.tp5.b1`
- consultez l'adresse MAC du client
- on peut consulter les adresses MAC des cartes réseau avec un simple `ip a` 
```
root@client3:/home/kevin/Desktop# ip a 
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:17:45:6a brd ff:ff:ff:ff:ff:ff
    inet 10.5.1.201/24 brd 10.5.1.255 scope global dynamic enp0s8
       valid_lft 42777sec preferred_lft 42777sec
    inet6 fe80::a00:27ff:fe17:456a/64 scope link 
       valid_lft forever preferred_lft forever
```
