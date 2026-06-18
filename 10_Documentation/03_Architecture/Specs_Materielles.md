# Spécifications matérielles et logicielles

## Spécifications du portable master (Ernest)

| Élément | Recommandation |
|---|---|
| Processeur | x86-64, virtualisation matérielle activée dans le BIOS (VT-x ou AMD-V) |
| RAM hôte minimale | 12 Go (4 Go pour Windows hôte, 8 Go pour la VM serveur FOG) |
| Stockage libre | 80 Go minimum pour la VM FOG et le dépôt d'images de test |
| Port Ethernet | RJ45 intégré ou adaptateur USB-Ethernet Gigabit |
| Wi-Fi | Actif en parallèle pour accès Internet |

## Spécifications des portables clients (Renan et Nicolas)

| Élément | Recommandation |
|---|---|
| Processeur | x86-64, virtualisation matérielle activée |
| RAM hôte minimale | 8 Go (4 Go pour Windows hôte, 4 Go pour la VM cliente) |
| Stockage libre | 40 Go minimum pour la VM cliente |
| Port Ethernet | RJ45 intégré ou adaptateur USB-Ethernet Gigabit |
| Wi-Fi | Actif en parallèle |

## Matériel réseau

| Élément | Recommandation |
|---|---|
| Switch | 8 ports Gigabit non managé, type TP-Link TL-SG108 ou équivalent |
| Câbles | 3 câbles Ethernet RJ45 catégorie 5e minimum, longueur 1 à 2 mètres |
| Adaptateurs USB-Ethernet | Selon besoin, Gigabit obligatoire |

## Logiciels à installer sur chaque poste

### Tous les postes

- VirtualBox 7.x (dernière version stable)
- VirtualBox Extension Pack
- Navigateur web récent pour accéder à l'interface FOG

### Poste master Ernest

- Image ISO Ubuntu Server 22.04 LTS
- FOG Project (installation depuis le script officiel)
- Image ISO Windows 10 Pro 22H2 (à conserver pour la capture initiale)

### Postes clients Renan et Nicolas

- Image ISO Windows 10 Pro 22H2 pour la création initiale de la VM source
- VirtualBox seul est suffisant pour les VMs cibles vides

## VMs à créer

### VM Serveur FOG (chez Ernest)

| Paramètre | Valeur |
|---|---|
| Système | Linux, Ubuntu 64-bit |
| RAM | 8 Go |
| vCPU | 2 |
| Disque | 80 Go dynamique |
| Réseau | Bridged sur interface Ethernet |
| Boot | UEFI désactivé, BIOS legacy pour simplicité |

### VM Source Windows 10 (chez Ernest, à utiliser pour la capture)

| Paramètre | Valeur |
|---|---|
| Système | Windows 10 64-bit |
| RAM | 4 Go |
| vCPU | 2 |
| Disque | 40 Go dynamique |
| Réseau | Bridged sur interface Ethernet |
| Boot | BIOS legacy |

### VMs Clientes cibles (chez Renan et Nicolas)

| Paramètre | Valeur |
|---|---|
| Système | Other or unknown 64-bit |
| RAM | 4 Go |
| vCPU | 2 |
| Disque | 40 Go dynamique, vide |
| Réseau | Bridged sur interface Ethernet |
| Boot | BIOS legacy, ordre de boot : Network en premier |
