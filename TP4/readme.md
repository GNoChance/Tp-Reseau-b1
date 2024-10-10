# TP4 : DHCP et accès internet

TP très court en rapport DHCP. DHCP c'est pour ~~Dynamic Host Configuration Protocol~~ permettre à nos mamans qui savent pas ce que c'est une adresse IP de quand même avoir un "accès internet".

## Sommaire

- [TP4 : DHCP et accès internet](#tp4--dhcp-et-accès-internet)
  - [Sommaire](#sommaire)
- [I. DHCP](#i-dhcp)
  - [1. Les mains dans le capot](#1-les-mains-dans-le-capot)

# I. DHCP

## 1. Les mains dans le capot

☀️ **Capturez un échange DHCP complet**

- capture `dhcp.pcap`
- il y a 4 trames (le DORA) : Discover, Offer, Request, Acknowledge
[Capture Wireshark dhcp](dhcp.pcap)


☀️ **Directement dans Wireshark, vous pouvez voir toutes les infos que vous donne  le serveur DHCP**

- retrouvez dans l'échange DHCP les 3 infos dont on parle plus haut :
  - adresse IP proposée
  - serveur DNS indiqué
  - passerelle du réseau
- **pour le compte-rendu** le nom des champs et leur valeur, pour ces 3 valeurs
  - par exemple (l'exemple te parlera + quand t'auras regardé dans Wireshark) :

Adresse IP proposée :

- Your (client) IP address: 0.0.0.0

Serveur DNS indiqué :

- Option: (6) Domain Name Server
  - Length: 8
  - Domaine Name Server: 8.8.8.8
  - Domaine Name Server: 1.1.1.1

Passerelle du réseau : 

- Option: (3) Router
  - Length : 4
  - Router : 10.33.79.254

