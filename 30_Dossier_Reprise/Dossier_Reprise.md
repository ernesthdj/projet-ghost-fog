# Dossier de reprise du projet — Projet Ghost (FOG)

> **Objet :** permettre à un technicien extérieur de reprendre ce projet sans nous avoir rencontrés.
> **Dernière mise à jour :** 18/06/2026 (Séance 3)
> **Groupe :** Hadjiu Ernest · Demeereleere Renan · Terroir Nicolas

> 📌 Ce document est un **point d'entrée synthétique**. Chaque section renvoie vers la
> documentation détaillée dans `10_Documentation/`. En cas de doute, la doc Markdown du vault fait foi.

---

## 1. Présentation du projet

### Objectif
Mettre en place un **serveur d'imagerie réseau** capable de **capturer** une image système Windows 10
de référence, puis de la **déployer automatiquement** sur plusieurs machines via le réseau, sans
réinstaller Windows manuellement sur chaque poste.

### Rôle du serveur Ghost (FOG)
« Ghost » désigne historiquement le logiciel de clonage de disques Norton Ghost. Ici, on utilise son
équivalent open source moderne : **FOG Project**. Le serveur FOG :
- distribue un **environnement de démarrage réseau (PXE)** aux machines clientes ;
- **capture** l'image disque d'une machine « master » et la stocke ;
- **déploie** cette image vers une ou plusieurs machines clientes ;
- s'administre via une **interface web**.

### Résultat attendu
Une VM cliente vide démarre sur le réseau, reçoit l'image Windows 10 depuis le serveur FOG, puis
redémarre sur un Windows fonctionnel — **sans intervention manuelle d'installation**. Le tout
documenté pour être reproductible par un tiers.

---

## 2. État d'avancement

### ✅ Ce qui a été réalisé
| # | Réalisation | Séance |
|---|---|---|
| 1 | Compréhension de l'imagerie réseau, choix de l'outil **FOG** justifié | S1 |
| 2 | Architecture cible définie, comparatif d'outils, analyse des risques | S1 |
| 3 | Hyper-V désactivé + VirtualBox installé sur les 3 PC | S2 |
| 4 | VM **Ubuntu 26.04 LTS** (`RenanVM`) créée et fonctionnelle | S2 |
| 5 | **FOG Project installé** en mode **proxyDHCP** (sans conflit avec le DHCP de la salle) | S2 |
| 6 | Interface web FOG accessible depuis les postes ; 1er hôte client enregistré | S2 |
| 7 | **Boot PXE validé sur CLIENT A** | S2 |
| 8 | Procédure d'installation v1 rédigée et testée | S2 |
| 9 | **Centralisation de la doc dans un dépôt Git** + réorganisation des dossiers | S3 |

### ⏳ Ce qui reste à faire
| #   | Tâche                                                              | Priorité | Responsable prévu |
| --- | ------------------------------------------------------------------ | -------- | ----------------- |
| 1   | Tester le boot PXE sur **CLIENT B** (reporté de S2)                | Haute    | Nicolas           |
| 2   | Créer la **VM Windows Master** + installer logiciels               | Haute    | Ernest            |
| 3   | Exécuter **Sysprep** (généraliser + arrêt sans redémarrage)        | Haute    | Ernest            |
| 4   | Enregistrer le master dans FOG + créer l'image + tâche **Capture** | Haute    | Ernest            |
| 5   | **Capturer** l'image Windows via FOG                               | Haute    | Ernest            |
| 6   | Créer les VMs clientes vides + tâches **Deploy**                   | Moyenne  | Renan / Nicolas   |
| 7   | **Déployer** l'image vers les clients + vérifier le démarrage      | Haute    | Tous              |
| 8   | **Exporter la VM FOG en `.ova`** (sauvegarde avant manip risquée)  | Haute    | Ernest            |
| 9   | Procédure de **restauration** en cas d'incident                    | Moyenne  | Nicolas           |
| 10  | Préparation de la **défense orale**                                | Moyenne  | Tous              |

> Plan détaillé et à jour : [`../10_Documentation/01_Pilotage/Plan_Attaque.md`](../10_Documentation/01_Pilotage/Plan_Attaque.md)

---

## 3. Procédures existantes

| Procédure | Statut | Emplacement |
|---|---|---|
| Désactivation de Hyper-V (prérequis VirtualBox) | ✅ Rédigée & testée | Livrable S2 §3.1 |
| Création + installation de la VM Ubuntu | ✅ Rédigée & testée | Livrable S2 §3.2–3.3 |
| Installation de FOG Project (mode proxyDHCP) | ✅ Rédigée & testée | Livrable S2 §3.4–3.5 |
| Enregistrement d'un hôte client dans FOG | ✅ Rédigée & testée | Livrable S2 §3.6 |
| **Procédure Master & Déploiement (pas-à-pas, clé en main)** | ✅ Rédigée, **à exécuter** | [`Procedure_Master_Deploiement.md`](../10_Documentation/05_Procedures/Procedure_Master_Deploiement.md) |
| Workflow complet capture → déploiement (vue d'ensemble) | ✅ Rédigé | [`Workflow_Deploiement_FOG.md`](../10_Documentation/05_Procedures/Workflow_Deploiement_FOG.md) |
| Préparation image via Sysprep | ⏳ Documentée, **à exécuter** | Procédure Master §Phase C |
| Capture / Déploiement via FOG | ⏳ Documentée, **à exécuter** | Procédure Master §Phases D-F |
| Restauration après incident | ❌ À rédiger | — |

> Le livrable PDF de la séance 2 (procédure d'installation complète avec captures) :
> [`../20_Livrables/Seance_02/`](../20_Livrables/Seance_02/)

### Paramètres clés à connaître (rappel pour un repreneur)
- **Désactiver Hyper-V** avant VirtualBox : `bcdedit /set hypervisorlaunchtype off` (admin) + redémarrage.
- **Nom d'interface réseau** Ubuntu = `enp0s3` (et **non** `eth0`) — toujours vérifier avec `ip a`.
- À l'install FOG, répondre **`N`** à « Ce serveur est-il le serveur DHCP ? » → évite le conflit avec le DHCP de la salle (mode **proxyDHCP**).
- Interface web FOG : `http://<IP-serveur>/fog/management` — identifiants par défaut `fog` / `password` (**à changer immédiatement**).

---

## 4. Problèmes rencontrés (journal des incidents)

| Incident | Cause | Solution | Statut |
|---|---|---|---|
| VM Ubuntu 64 bits ne démarre pas dans VirtualBox | Hyper-V actif sur Windows 11 monopolise la virtualisation (VT-x) | `bcdedit /set hypervisorlaunchtype off` (admin) + redémarrage | ✅ Résolu (S2) |
| Nom d'interface réseau refusé par le script FOG | Ubuntu utilise le nommage systemd : `enp0s3` ≠ `eth0` | Vérifier avec `ip a` avant de lancer le script, saisir le nom exact | ✅ Résolu (S2) |
| Doc dépendante d'une seule personne (Nico absent, Ernest auditeur) | Documentation non centralisée, locale au bureau de Renan | Mise en place d'un **dépôt Git partagé** + réorganisation des dossiers | ✅ Résolu (S3) |

---

## 5. Analyse des risques (synthèse)

### Risques techniques
- Image corrompue à la capture/déploiement → vérif d'intégrité + test sur poste pilote.
- Conflit DHCP FOG ↔ DHCP de la salle → **maîtrisé** (proxyDHCP, réponse `N`).
- PXE non fonctionnel sur un client → validé CLIENT A, **CLIENT B à tester (S3)**.
- Sysprep mal exécuté (SID dupliqués) / VM master redémarrée après Sysprep → procédure stricte, snapshot avant.
- Capture interrompue → export `.ova` de la VM FOG avant toute manip risquée.

### Risques organisationnels
- **Absence d'un membre** → désormais mitigé par le dépôt Git (continuité assurée).
- Mauvaise procédure / étapes manquantes → relecture croisée par le Responsable Qualité.
- Perte de la doc (panne d'un portable) → Git distant + OneDrive.

### Mesures préventives clés
Snapshot/`.ova` avant manip risquée · changement du mot de passe FOG par défaut · checklist de
préparation en début de séance · `git pull`/`push` systématique.

> Tableau complet : [`../10_Documentation/04_Risques_Checklists/Analyse_Risques.md`](../10_Documentation/04_Risques_Checklists/Analyse_Risques.md)

---

## 6. Travail restant (prochaines séances)

Voir le tableau §2 « Ce qui reste à faire ». **Priorités immédiates (S3) :**
1. Boot PXE CLIENT B (Nico).
2. VM Windows Master + Sysprep (Renan).
3. Première capture d'image via FOG (Ernest).
4. Export `.ova` de la VM FOG **avant** toute manipulation risquée.

---

## 7. Glossaire

| Terme | Définition |
|---|---|
| **Ghost / FOG** | Logiciel de clonage et de déploiement d'images disque par le réseau. FOG (Free Open Ghost) est l'alternative open source utilisée ici. |
| **Image système** | Copie complète d'un disque (OS + logiciels + config) pouvant être redéployée à l'identique sur d'autres machines. |
| **PXE** (*Preboot eXecution Environment*) | Mécanisme permettant à une machine de **démarrer depuis le réseau** au lieu de son disque, pour recevoir ou envoyer une image. |
| **DHCP** (*Dynamic Host Configuration Protocol*) | Service qui attribue automatiquement les adresses IP sur un réseau. |
| **proxyDHCP** | Mode où FOG fournit **uniquement** l'info de démarrage PXE, **sans** distribuer d'adresses IP — pour cohabiter avec un DHCP existant (celui de la salle). |
| **TFTP** (*Trivial File Transfer Protocol*) | Protocole léger utilisé par PXE pour transférer le fichier de démarrage initial. |
| **Sysprep** (*System Preparation Tool*) | Outil Windows qui « généralise » une image : suppression du SID et des identifiants uniques, pour qu'elle soit déployable sur plusieurs machines. |
| **SID** (*Security Identifier*) | Identifiant unique d'une installation Windows. Des SID dupliqués causent des conflits sur un parc — d'où Sysprep. |
| **VM** (*Virtual Machine*) | Machine virtuelle : ordinateur émulé par un logiciel (ici VirtualBox). |
| **Hyperviseur** | Logiciel qui exécute des VMs. Ici : **VirtualBox 7.x**. |
| **Bridged / NAT / Internal Network** | Modes réseau VirtualBox. *Bridged* = la VM est visible sur le LAN physique ; *NAT* = la VM partage l'IP de l'hôte (non joignable de l'extérieur) ; *Internal Network* = réseau privé entre VMs d'un même hôte. |
| **`.ova`** | Format d'export d'une VM complète (un fichier), pour sauvegarde ou transfert. |
| **Master** | Machine de référence dont on capture l'image avant déploiement. |

---

## 8. Points à vérifier / clarifier au démarrage d'une reprise

- [ ] **Mode réseau réel de la VM FOG** : la procédure mentionne *Bridged*, mais une capture S2 montre l'IP `10.0.2.15` (adresse NAT par défaut de VirtualBox). À confirmer via *VirtualBox → Config → Réseau* + `ip a`, car cela conditionne le PXE entre PC physiques distincts.
- [ ] Version d'Ubuntu : la planif initiale visait 22.04 LTS, la réalité S2 est **26.04 LTS**. La doc est en cours d'harmonisation sur 26.04.
- [ ] Adresses MAC des VMs clientes à revérifier avant chaque séance (enregistrement FOG).
