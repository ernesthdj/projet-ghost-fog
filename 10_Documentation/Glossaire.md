# Glossaire – Projet Ghost Server (FOG)

> **Projet :** Ghost Server – Déploiement automatisé par PXE/FOG  
> **Groupe :** Ernest Hadjiu · Renan Demeereleere · Nicolas Terroir  
> **Cours :** Systèmes d'exploitation – IPAMC Ecaussinnes · Mme ROBBE · 2025-2026  

---

## A

**Adaptateur réseau en pont (Bridged Adapter)**  
Mode réseau de VirtualBox dans lequel la machine virtuelle est connectée directement au réseau physique de l'hôte, comme si elle était une machine physique à part entière. Indispensable pour que le PXE fonctionne entre les VMs du groupe.

**Analyse des risques**  
Document identifiant les problèmes potentiels pouvant survenir lors du projet, leur probabilité, leur impact et les mesures préventives associées.

---

## B

**BIOS (Basic Input/Output System)**  
Firmware de démarrage d'un ordinateur, antérieur à l'UEFI. Utilisé dans ce projet car FOG et le PXE sont plus compatibles en mode BIOS legacy qu'en mode UEFI.

**Boot PXE**  
Démarrage d'une machine via le réseau plutôt que depuis un disque local. La machine contacte un serveur DHCP/proxyDHCP qui lui fournit un bootloader (iPXE), puis charge le menu FOG.

**Bridged Adapter**  
Voir *Adaptateur réseau en pont*.

---

## C

**Capture d'image**  
Opération FOG consistant à copier le contenu du disque d'une VM Master (après Sysprep) vers le serveur FOG, pour pouvoir la redéployer sur d'autres machines.

**Checklist**  
Document de contrôle listant les étapes ou critères à valider pour s'assurer qu'une procédure a été correctement exécutée.

**CLIENT A**  
VM cliente PXE hébergée sur le portable de Renan. Boot réseau uniquement, disque vide, reçoit l'image déployée par FOG.

**CLIENT B**  
VM cliente PXE hébergée sur le portable de Nicolas. Configurée en S3 (8 Go RAM, 40 Go VDI, Bridged, boot réseau en premier, BIOS legacy).

---

## D

**Déploiement d'image**  
Opération FOG consistant à écrire une image capturée sur le disque d'une VM cliente via PXE. Equivalent d'une installation Windows automatisée sur plusieurs machines simultanément.

**DHCP (Dynamic Host Configuration Protocol)**  
Protocole réseau permettant d'attribuer automatiquement une adresse IP à une machine. Dans ce projet, le DHCP est assuré par la salle IPAMC ; FOG fonctionne en mode proxyDHCP pour ne pas créer de conflit.

**Dossier de reprise**  
Document permettant à une équipe externe de reprendre le projet sans assistance de l'équipe d'origine. Contient l'état du projet, les procédures et les contacts.

---

## E

**enp0s3**  
Nom de l'interface réseau principale de la VM Ubuntu (ErnestVM/RenanVM) sous VirtualBox. Configurée en DHCP via Netplan.

**ErnestVM**  
Machine virtuelle Ubuntu hébergée sur le portable d'Ernest, destinée à accueillir le serveur FOG en configuration finale. Configurée en Bridged Adapter, mode proxyDHCP.

---

## F

**Firmware**  
Logiciel bas niveau intégré au matériel, responsable du démarrage de la machine. Peut être BIOS (legacy) ou UEFI. Ce projet utilise BIOS legacy pour la compatibilité PXE/FOG.

**FOG Project (Free Open-source Ghost)**  
Solution open-source de déploiement d'images disque via le réseau (PXE/TFTP). Installée sur Ubuntu, elle permet de capturer et déployer des images Windows sur plusieurs machines clientes.

**FOG Management**  
Interface web d'administration de FOG, accessible via `http://[IP-FOG]/fog/management`. Permet de gérer les hôtes, images, tâches de capture et de déploiement.

---

## G

**Ghost Server**  
Nom du projet : serveur de déploiement automatisé d'images système via FOG/PXE. Le terme "ghost" fait référence au clonage de disque (comme le logiciel Norton Ghost).

**GitHub**  
Plateforme de versioning utilisée par le groupe pour centraliser la documentation du projet. Dépôt : `ernesthdj/projet-ghost-fog`.

---

## H

**Hôte (Host)**  
Machine physique sur laquelle tourne VirtualBox. Dans ce projet : portable Ernest (hôte du serveur FOG), portable Renan (hôte CLIENT A), portable Nicolas (hôte CLIENT B).

---

## I

**Image disque**  
Copie complète du contenu d'un disque dur, incluant le système d'exploitation, les pilotes et les configurations. FOG capture et déploie ces images via le réseau.

**IPAMC**  
Institut de Promotion des Activités Mécaniques et de la Carrosserie — établissement scolaire d'Ecaussinnes où se déroule le projet.

**iPXE**  
Implémentation open-source du protocole PXE utilisée par FOG. Charge le menu de démarrage réseau sur les VM clientes.

**IP statique / IP DHCP**  
Adresse IP fixe (statique) ou attribuée automatiquement (DHCP). La VM serveur FOG reçoit une IP DHCP de la salle à chaque séance — ce changement d'IP est un risque identifié dans le projet.

---

## J

**Journal Agile**  
Document de suivi des sprints du projet : kanban, vélocité, rétrospective. Utilisé pour piloter l'avancement selon la méthode Scrum.

**Journal des changements**  
Document traçant toutes les modifications apportées au projet (Num, Modifications, Auteurs, Date, Justifications).

**Journal des incidents**  
Document consignant les problèmes rencontrés (Num, Date, Problème, Impact, Cause, Solution, Validation). Voir aussi *Fiche anomalie*.

---

## L

**LAN (Local Area Network)**  
Réseau local. Dans ce projet, le LAN de la salle IPAMC relie tous les portables du groupe et le serveur FOG via un switch.

**Legacy BIOS**  
Voir *BIOS*.

---

## M

**Master (VM Master)**  
Machine virtuelle Windows 10 Pro 22H2 préparée par Ernest, sur laquelle est installé et configuré l'OS avant capture. Doit être passée par Sysprep avant la capture FOG.

**Méthode Scrum**  
Méthode de gestion de projet Agile basée sur des sprints courts, des rôles définis (Scrum Master, équipe) et des cérémonies (rétrospective, daily). Appliquée dans ce projet.

---

## N

**Netplan**  
Outil de configuration réseau sous Ubuntu. Utilisé pour activer le DHCP sur l'interface `enp0s3` de la VM serveur FOG.

---

## O

**OVA (.ova)**  
Format d'export d'une machine virtuelle VirtualBox. Permet de sauvegarder et transférer une VM complète (disque + configuration). Prévu pour exporter ErnestVM en S4.

---

## P

**PC MASTER**  
Portable physique d'Ernest, hébergeant la VM serveur FOG (ErnestVM) et la VM Windows Master.

**Plan de tests**  
Document décrivant les scénarios de test à exécuter pour valider le bon fonctionnement du système (PXE boot, capture, déploiement, SID...).

**Planification**  
Ensemble des documents organisant le travail dans le temps : `Plan_Attaque.md`, `Journal_Agile.md`, tableau de bord.

**proxyDHCP**  
Mode de fonctionnement de FOG dans lequel il répond aux requêtes PXE sans gérer lui-même les adresses IP (le DHCP de la salle garde ce rôle). Activé en répondant "N" au script d'installation FOG. Évite les conflits réseau en salle.

**PXE (Preboot eXecution Environment)**  
Protocole permettant à une machine de démarrer depuis le réseau. La VM cliente envoie une requête DHCP au démarrage, obtient l'adresse du serveur FOG, télécharge le bootloader iPXE via TFTP et affiche le menu FOG.

---

## R

**Rapport de séance**  
Document produit à chaque séance, contenant : membres présents, objectifs, tâches réalisées, difficultés, travail restant.

**Registre des tests**  
Document enregistrant les résultats des tests effectués : date, testeur, résultat, observations.

**Rollback**  
Opération consistant à revenir à un état antérieur stable après une erreur. Dans VirtualBox, réalisé via les snapshots.

---

## S

**SID (Security Identifier)**  
Identifiant unique Windows attribué à chaque installation. Sysprep réinitialise le SID avant capture pour éviter les conflits lors du déploiement sur plusieurs machines.

**Snapshot**  
Sauvegarde de l'état d'une VM à un instant donné dans VirtualBox. Permet de revenir en arrière rapidement en cas d'erreur (rollback). Obligatoire avant tout Sysprep.

**Scrum Master**  
Rôle dans la méthode Scrum : facilite le travail de l'équipe, lève les obstacles, coordonne les sprints. Tenu par Ernest dans ce projet.

**Sprint**  
Itération de travail délimitée dans le temps (dans ce projet = 1 séance). Le projet comporte 4 sprints (S1 à S4).

**Sysprep (System Preparation Tool)**  
Outil Microsoft qui généralise une installation Windows avant capture : supprime les identifiants uniques (SID, nom de machine) pour rendre l'image déployable sur n'importe quelle machine. Opération **irréversible** — un snapshot `avant-sysprep` est obligatoire.

---

## T

**TFTP (Trivial File Transfer Protocol)**  
Protocole de transfert de fichiers simplifié utilisé par FOG pour envoyer le bootloader iPXE aux clients PXE. Fonctionne sur le port UDP 69.

**Tableau de bord**  
Document de suivi global du projet : KPIs, avancement des tâches, statut par séance.

---

## U

**Ubuntu Server 22.04 LTS**  
Distribution Linux utilisée comme OS du serveur FOG (ErnestVM). Version LTS (Long Term Support) = support garanti 5 ans.

**UEFI (Unified Extensible Firmware Interface)**  
Successeur moderne du BIOS. **Non utilisé dans ce projet** car moins compatible avec le PXE/FOG en environnement VirtualBox. L'option "Activer EFI" doit être décochée dans VirtualBox.

---

## V

**VDI (VirtualBox Disk Image)**  
Format de disque virtuel propriétaire VirtualBox. Utilisé en mode "dynamiquement alloué" dans ce projet (le fichier grandit au fur et à mesure).

**VirtualBox**  
Hyperviseur open-source d'Oracle utilisé par les trois membres du groupe pour créer et gérer les machines virtuelles. Version 7.x.

**VM (Machine Virtuelle)**  
Ordinateur émulé logiciellement par VirtualBox, isolé du système hôte. Ce projet utilise plusieurs VMs : serveur FOG (ErnestVM), VM Master Windows, VM Cliente A (Renan), VM Cliente B (Nicolas).

---

## W

**Windows 10 Pro 22H2**  
Version de Windows utilisée dans ce projet comme OS cible à déployer via FOG. Installée proprement sur la VM Master, capturée après Sysprep, puis déployée sur les VMs clientes.

---

*Glossaire rédigé par Nicolas Terroir – Séance 3/4 · IPAMC 2025-2026*
