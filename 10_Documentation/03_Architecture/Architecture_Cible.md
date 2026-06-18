# Architecture cible

## Description générale

L'architecture du lab repose sur trois portables étudiants reliés à un switch 8 ports physique, formant un segment réseau isolé du réseau de l'école. Sur le portable d'Ernest s'exécute la VM serveur FOG. Sur les portables de Renan et Nicolas s'exécute chacun une VM cliente Windows 10 destinée à recevoir l'image déployée.

Le réseau du lab est piloté par FOG, qui assure le service DHCP et le service TFTP nécessaires au démarrage PXE des clients.

## Composants matériels

| Composant | Description |
|---|---|
| Portable Ernest | Hôte du serveur FOG en VM, environ 8 Go RAM dédiés à la VM |
| Portable Renan | Hôte d'une VM cliente Windows 10, 4 Go RAM dédiés à la VM |
| Portable Nicolas | Hôte d'une VM cliente Windows 10, 4 Go RAM dédiés à la VM |
| Switch 8 ports | Switch Gigabit non managé, segment isolé |
| Câbles Ethernet | 3 câbles RJ45 catégorie 5e minimum |
| Adaptateurs USB-Ethernet | Selon disponibilité des ports RJ45 sur les portables |

## Composants logiciels

| Composant | Rôle |
|---|---|
| Ubuntu Server 22.04 LTS | OS support de la VM serveur FOG |
| FOG Project (dernière version stable) | Serveur de déploiement d'image |
| MySQL ou MariaDB | Base de données interne de FOG |
| Apache | Serveur web pour l'interface d'administration FOG |
| TFTP | Service de démarrage PXE |
| Windows 10 Pro 22H2 | OS cible à déployer |
| VirtualBox 7.x | Hyperviseur sur les trois portables |

## Logique de fonctionnement

1. Les trois portables sont branchés au switch via leur interface Ethernet.
2. Chaque VirtualBox est configuré en mode Bridged sur l'interface Ethernet de l'hôte.
3. La VM serveur FOG démarre et expose son interface web sur une IP fixe du segment isolé.
4. Une VM cliente est créée vide sur le portable de Renan ou Nicolas.
5. La VM cliente est configurée pour démarrer en PXE.
6. Au démarrage, la VM cliente contacte le serveur FOG qui lui fournit le bootloader PXE.
7. L'administrateur déclenche depuis l'interface FOG soit une capture (la VM cliente envoie son image au serveur), soit un déploiement (le serveur envoie une image existante à la VM cliente).
8. Une fois le transfert terminé, la VM cliente redémarre sur son disque local avec le système déployé.

## Adressage logique prévu

| Élément | Adresse IP | Rôle |
|---|---|---|
| Serveur FOG | 192.168.50.10 | Statique |
| Pool DHCP clients | 192.168.50.100 à 192.168.50.200 | Distribué par FOG |
| Passerelle | 192.168.50.1 (non utilisée, segment isolé) | Pour mémoire |
| Masque | 255.255.255.0 | / 24 |

Adressage à valider lors de l'installation effective. Les valeurs peuvent être ajustées sans impact sur la documentation produite.
