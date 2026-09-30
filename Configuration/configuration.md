# Configuration du lab MikroTik

## 1. Configuration initiale

Le laboratoire a été réalisé avec MikroTik RouterOS dans un environnement virtualisé VMware.

L'objectif est de reproduire progressivement le fonctionnement d'un routeur MikroTik dans un environnement réseau proche d'un scénario réel.

### 1.1 Environnement

- Hyperviseur : VMware
- Routeur : MikroTik RouterOS
- Outil d'administration : WinBox
- Environnement client : Windows
- Réseau de management : VMnet1 (Host-Only)
- Réseau WAN : VMnet8


### 2. Configuration du réseau de management

## 2.1 Objectif

Un réseau de management dédié a été mis en place afin de permettre l'administration du routeur MikroTik depuis le PC hôte, indépendamment du réseau utilisé par les clients du HotSpot.

Dans le laboratoire virtualisé, ce réseau repose sur VMware VMnet1 en mode Host-Only.

Cette séparation permet de distinguer clairement :

- le réseau utilisé pour administrer le MikroTik ;
- le réseau utilisé par les clients du HotSpot ;
- le réseau WAN utilisé pour l'accès vers l'extérieur.

Le réseau de management n'est donc pas destiné à fournir l'accès Internet aux clients. Il sert principalement à établir la communication entre le PC hôte et le routeur MikroTik pour les besoins d'administration.

---

2.2 Architecture du réseau de management

Le réseau de management est organisé de la manière suivante :

PC hôte
   │
   │ VMware Network Adapter VMnet1
   │ 192.168.162.1/24
   │
   ▼
VMnet1 — Host-Only
   │
   │ 192.168.162.0/24
   │
   ▼
ether2
MikroTik
192.168.162.254/24

Dans cette architecture, "ether2" est dédié au management du MikroTik.

L'adresse "192.168.162.254/24" est utilisée comme adresse IP de management du routeur.

Le PC hôte utilise le réseau "192.168.162.0/24" pour communiquer directement avec cette interface, sans passer par le réseau HotSpot.

---

## 2.3 Configuration de l'adresse IP

L'adresse IP de management est attribuée à l'interface "ether2" avec la commande suivante :

/ip address add address=192.168.162.254/24 interface=ether2

Cette configuration permet à l'interface "ether2" de participer au réseau :

192.168.162.0/24

L'adresse du MikroTik sur ce réseau est :

192.168.162.254

Cette adresse est ensuite utilisée pour les opérations d'administration et les tests de connectivité depuis le PC hôte.

Capture d'écran à intégrer :

![Adresse IP du réseau de management](../screenshots/configuration/management-ip.png)

 Adresse IP configurée sur l'interface de management.

---

## 2.4 Vérification de l'adresse IP

La configuration des adresses IP du MikroTik peut être vérifiée avec :

/ip address print

L'entrée correspondant à "ether2" doit apparaître avec l'adresse :

192.168.162.254/24

Cette vérification permet de confirmer que l'interface de management possède bien l'adresse attendue.

Capture d'écran à intégrer :

![Vérification de l'adresse de management](../screenshots/configuration/management-ip-verification.png)

— Vérification de l'adresse IP du réseau de management.

---

## 2.5 Accès au MikroTik depuis le PC hôte

Une fois le réseau "VMnet1" opérationnel, le PC hôte peut communiquer avec le MikroTik via son adresse de management.

Un test de connectivité peut être effectué depuis le PC hôte avec :

ping 192.168.162.254

Une réponse correcte confirme que la communication IP entre le PC hôte et l'interface "ether2" du MikroTik est fonctionnelle.

Cette vérification constitue un premier contrôle avant l'utilisation des outils d'administration tels que WinBox ou les accès en ligne de commande.

Capture d'écran à intégrer :

![Test de connectivité entre le PC hôte et le MikroTik](../screenshots/configuration/management-ping.png)

— Test de connectivité entre le PC hôte et le MikroTik.

---

## 2.6 Configuration du DHCP du réseau de management

Un serveur DHCP a également été configuré sur "ether2" afin de permettre l'attribution automatique d'adresses IP aux équipements connectés au réseau de management.

Le plan d'adressage retenu est le suivant :

Élément| Valeur
Réseau| "192.168.162.0/24"
MikroTik| "192.168.162.254"
Pool DHCP| "192.168.162.10 - 192.168.162.100"
Interface| "ether2"

## 2.6.1 Création du pool DHCP

Le pool d'adresses utilisé par le serveur DHCP est créé avec :

/ip pool add name=pool-management ranges=192.168.162.10-192.168.162.100

Ce pool définit la plage d'adresses pouvant être attribuées automatiquement aux clients du réseau de management.

---

## 2.6.2 Création du réseau DHCP

Les paramètres du réseau DHCP sont ensuite définis avec :

/ip dhcp-server network add address=192.168.162.0/24 gateway=192.168.162.254

La passerelle distribuée aux clients est donc l'adresse IP de l'interface "ether2" du MikroTik :

192.168.162.254

---

## 2.6.3 Création du serveur DHCP

Le serveur DHCP est associé à l'interface "ether2" :

/ip dhcp-server add name=dhcp-management interface=ether2 address-pool=pool-management disabled=no

Le fonctionnement est alors le suivant :

Client
   │
   │ DHCP
   ▼
ether2
MikroTik
   │
   ├── Adresse distribuée :
   │   192.168.162.10 - 192.168.162.100
   │
   └── Passerelle :
       192.168.162.254

---

## 2.7 Vérification du serveur DHCP

La présence du serveur DHCP peut être vérifiée avec :

/ip dhcp-server print

Le serveur "dhcp-management" doit apparaître comme actif sur l'interface "ether2".

Le pool DHCP peut être vérifié avec :

/ip pool print

Une attribution d'adresse à un client peut également être vérifiée avec :

/ip dhcp-server lease print

Cette commande permet notamment de vérifier qu'un équipement connecté au réseau de management a bien obtenu une adresse située dans la plage :

192.168.162.10 - 192.168.162.100

Capture d'écran à intégrer :

![Serveur DHCP du réseau de management](../screenshots/configuration/management-dhcp.png)

— Configuration du serveur DHCP sur le réseau de management.

---

## 2.8 Résultat de la configuration

À l'issue de cette configuration, le MikroTik dispose d'un réseau de management séparé du réseau HotSpot.

Le fonctionnement obtenu est le suivant :

                    LABORATOIRE VIRTUALISÉ

        PC hôte
           │
           │ VMnet1 — Host-Only
           │ 192.168.162.0/24
           │
           ▼
      ┌───────────────┐
      │    MikroTik   │
      │               │
      │ ether2        │
      │ 192.168.162.254
      └───────────────┘

        Réseau dédié à
        l'administration

Le réseau "192.168.162.0/24" est ainsi utilisé exclusivement pour les besoins de management du routeur, tandis que le réseau destiné aux clients du HotSpot reste indépendant.

Cette séparation constitue la base du reste de la configuration du lab : le management, le HotSpot et le WAN sont traités comme des réseaux distincts.


## 3. Configuration du réseau WAN

## 3.1 Objectif

L'interface "ether1" du MikroTik est utilisée comme interface WAN.

Elle assure la connexion du routeur vers le réseau extérieur à travers le réseau NAT de VMware.

Cette interface est distincte du réseau de management configuré sur "ether2" et du réseau destiné aux clients du HotSpot.

L'architecture retenue est donc :

                    PC hôte
                       │
                VMware Workstation
                       │
                Réseau NAT VMware
                       │
                       │ WAN
                       ▼
                  ether1
                MikroTik

Le rôle d'"ether1" est principalement de permettre au MikroTik d'obtenir une connectivité vers l'extérieur et, par la suite, de fournir cette connectivité au réseau interne.

---

## 3.2 Raccordement de l'interface WAN

Dans VMware Workstation, la carte réseau virtuelle correspondant à "ether1" est connectée à un réseau NAT.

Le mode NAT permet à la machine virtuelle MikroTik d'accéder au réseau externe en utilisant la connectivité du PC hôte.

Le MikroTik ne reçoit donc pas ici une adresse IP statique définie manuellement. Il récupère ses paramètres réseau auprès du serveur DHCP fourni par le réseau NAT de VMware.

---

## 3.3 Configuration du client DHCP sur "ether1"

Le client DHCP de MikroTik est activé sur l'interface "ether1" avec la commande :

/ip dhcp-client add interface=ether1 disabled=no

Le MikroTik peut ainsi recevoir automatiquement :

- une adresse IP WAN ;
- un masque réseau ;
- une passerelle par défaut ;
- éventuellement les informations DNS fournies par le réseau DHCP.

L'adresse obtenue dépend du réseau NAT configuré dans VMware et peut donc varier.

---

## 3.4 Vérification de l'adresse WAN

La configuration du client DHCP peut être vérifiée avec :

/ip dhcp-client print detail

L'interface "ether1" doit apparaître avec un état indiquant que le bail DHCP a été obtenu.

L'adresse attribuée à l'interface peut également être vérifiée avec :

/ip address print

L'adresse affichée sur "ether1" correspond alors à l'adresse fournie dynamiquement par le réseau NAT de VMware.

Capture d'écran à intégrer :

![Adresse IP WAN du MikroTik](../screenshots/configuration/wan-ip.png)

 — Adresse IP obtenue dynamiquement sur l'interface WAN "ether1".

---

## 3.5 Vérification de la route par défaut

L'utilisation du DHCP sur "ether1" permet également au MikroTik d'apprendre la passerelle du réseau WAN.

La table de routage peut être vérifiée avec :

/ip route print

Une route par défaut doit être présente sous la forme :

0.0.0.0/0

Cette route indique au MikroTik quelle passerelle utiliser lorsqu'une destination ne se trouve pas dans l'un de ses réseaux directement connectés.

Capture d'écran à intégrer :

![Route par défaut du MikroTik](../screenshots/configuration/wan-route.png)

— Vérification de la route par défaut vers le réseau WAN.

---

## 3.6 Test de connectivité vers l'extérieur

Une fois l'adresse WAN et la route par défaut obtenues, la connectivité vers l'extérieur peut être vérifiée directement depuis le MikroTik.

Un test peut être réalisé avec :

/ping 8.8.8.8

Une réponse indique que le MikroTik est capable de joindre une adresse IP située à l'extérieur de son réseau local.

Le test par adresse IP est utilisé ici afin de vérifier la connectivité réseau indépendamment de la résolution DNS.

Capture d'écran à intégrer :

![Test de connectivité WAN](../screenshots/configuration/wan-ping.png)

— Test de connectivité du MikroTik vers l'extérieur.

---

## 3.7 Vérification de la résolution DNS

Après avoir vérifié la connectivité IP, la résolution DNS peut être testée depuis le MikroTik.

Par exemple :

/ping google.com

Si le nom de domaine est résolu et que les paquets reçoivent une réponse, cela permet de vérifier à la fois :

Connectivité IP
       +
Résolution DNS

La configuration DNS du routeur peut être consultée avec :

/ip dns print

Capture d'écran à intégrer :

![Test DNS depuis le MikroTik](../screenshots/configuration/wan-dns-test.png)

— Vérification de la résolution DNS depuis le MikroTik.

---

## 3.8 Résultat de la configuration

À ce stade, le MikroTik dispose d'une connectivité WAN fonctionnelle sur "ether1".

L'architecture obtenue est la suivante :

                         INTERNET
                            │
                            │
                     VMware NAT
                            │
                            ▼
                       ether1
                     ┌─────────┐
                     │ MikroTik│
                     └─────────┘
                       ▲     ▲
                       │     │
                  ether2     ether3
                 Management   LAN /
                              HotSpot
                       │
                 VMnet1 Host-Only
                 192.168.162.0/24

Le réseau WAN est ainsi séparé du réseau de management. L'interface "ether1" sert à la communication vers l'extérieur, tandis que "ether2" reste dédiée à l'administration du routeur depuis le PC hôte.

Cette connectivité WAN constitue la base nécessaire pour la configuration du réseau interne et du service HotSpot dans les étapes suivantes.
