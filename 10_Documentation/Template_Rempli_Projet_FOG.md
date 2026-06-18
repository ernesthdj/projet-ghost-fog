# Template rempli — Projet Ghost / FOG

> Document consolidé répondant au modèle fourni par l'enseignante.
> Toutes les sections sont alimentées depuis la documentation du dépôt `projet-ghost-fog`.
> Date de rédaction : 18/06/2026

---

# Modèle de rapport de séance

## Rapport de séance 3 — 18/06/2026

### Membres du groupe et fonctions

| Prénom + nom | Fonction | Présent(e) |
|---|---|---|
| Hadjiu Ernest | Coordinateur — Responsable Documentation | Oui |
| Demeereleere Renan | Technicien Infrastructure | Oui |
| Terroir Nicolas | Responsable Qualité et Tests | À confirmer en séance |

### Objectifs et ordre du jour de la séance

Objectifs fixés en début de séance (issus du plan d'attaque) :

1. Vérifier que chaque membre comprend le projet dans son état actuel (objectifs, avancement, blocages, suite).
2. Centraliser l'ensemble de la documentation dans le dépôt GitHub partagé `projet-ghost-fog`.
3. Réorganiser l'arborescence des dossiers pour la rendre claire et reproductible.
4. Rédiger le **Dossier de reprise** (`30_Dossier_Reprise/Dossier_Reprise.md`).
5. Poursuivre les travaux techniques : PXE CLIENT B, VM Windows Master, Sysprep, capture d'image.

### Tâches réalisées, détails et synthèse de la discussion

| Tâche | Responsable | Détail |
|---|---|---|
| Réorganisation complète de l'arborescence du projet | Ernest | Renommage des dossiers en convention numérotée `00_` → `90_` pour un classement cohérent |
| Centralisation de la documentation S2 | Ernest / Renan | Rapatriement du travail de S2 (jusque-là dans un PDF) dans le vault Markdown ; correction des incohérences (Ubuntu 26.04 LTS, mode proxyDHCP) |
| Création du Dossier de reprise | Ernest | Rédaction de `30_Dossier_Reprise/Dossier_Reprise.md` avec glossaire et instructions de continuité |
| Mise en place du dépôt Git + GitHub | Ernest | Dépôt `projet-ghost-fog` créé sur GitHub, collaborateurs Renan et Nicolas ajoutés |
| *(Volet technique — à compléter en séance)* | Renan / Nicolas | PXE CLIENT B · VM Windows Master · Sysprep · Capture image |

**Synthèse de la discussion :** La séance a été centrée sur la **continuité du projet**. Le constat clé de S2 — tout reposait sur Renan, la documentation n'était pas partagée — a conduit à prioriser la mise en place du dépôt Git avant toute avancée technique. La décision est prise : la documentation est la condition de viabilité du projet.

### Difficultés rencontrées et décisions prises

| Difficulté | Décision prise |
|---|---|
| Documentation éparpillée, format PDF non modifiable facilement | Migration complète vers Markdown dans un dépôt Git partagé |
| Incohérences dans la version d'Ubuntu documentée | Vérification sur la VM réelle et harmonisation (Ubuntu 26.04 LTS confirmé) |
| Risque d'absence de membres sans filet de sécurité documentaire | Règle : tout commit après chaque séance, `git pull` obligatoire en début de séance |

### Travail restant

Actions à faire avant la prochaine séance :

- [ ] Tester le **boot PXE sur CLIENT B** (Nicolas)
- [ ] Créer la **VM Windows Master** (Ernest) : 4 Go RAM, 40 Go disque, réseau même mode que FOG, boot Réseau en 1er
- [ ] Installer Windows 10 Pro et les logiciels souhaités sur le master
- [ ] Faire le **snapshot `avant-sysprep`** puis exécuter Sysprep
- [ ] Enregistrer le master dans FOG (MAC) et lancer la **capture d'image**
- [ ] Exporter la **VM FOG en `.ova`** (sauvegarde serveur)
- [ ] Mettre à jour le rapport de séance 3 en fin de séance

---

# Modèle de procédure technique

## Procédure d'installation de FOG Project sur VirtualBox (Ubuntu Server)

### Objectif de la procédure

**Qui :** Technicien Infrastructure (Renan) ou tout membre avec droits administrateur.
**Quoi :** Installer et configurer un serveur de déploiement d'images réseau (FOG Project) dans une VM Ubuntu sous VirtualBox.
**Quand :** En début de projet, avant toute capture ou déploiement d'image Windows.
**Comment :** Via le script d'installation officiel de FOG, en mode proxyDHCP pour coexister avec le DHCP de la salle.

### Prérequis

- VirtualBox 7.x installé sur le PC hôte (Hyper-V **désactivé** : `bcdedit /set hypervisorlaunchtype off` en admin + redémarrage)
- ISO **Ubuntu Server 26.04 LTS** disponible sur le disque hôte
- ISO **Windows 10 Pro 22H2** disponible (pour la capture ultérieure)
- Au moins **50 Go libres** sur le disque hôte
- Accès internet depuis la VM (pour télécharger FOG via le script officiel)
- Droits administrateur sur le PC hôte

### Étapes

#### Installation de la VM Ubuntu (serveur FOG)

- [ ] **1.1** — Créer une VM dans VirtualBox :

| Paramètre | Valeur |
|---|---|
| Nom | `FOG-Server` (ou `RenanVM`) |
| Type / Version | Linux — Ubuntu (64-bit) |
| RAM | 4 096 Mo minimum |
| Disque | 50 Go — allocation dynamique (`.vdi`) |
| Réseau — Carte 1 | **Bridged** sur l'interface Ethernet physique du PC hôte |

- [ ] **1.2** — Monter l'ISO Ubuntu Server sur le lecteur optique virtuel.

#### Paramétrage du serveur

- [ ] **2.1** — Démarrer la VM et installer Ubuntu Server (sélectionner **OpenSSH** lors de l'installation).
- [ ] **2.2** — Créer l'utilisateur `renan` (ou nom du technicien), définir un mot de passe fort.
- [ ] **2.3** — Vérifier le nom d'interface réseau réel de la VM : `ip a` → noter le nom (ex. `enp0s3`).
- [ ] **2.4** — Configurer une IP fixe sur la VM FOG (`192.168.50.10/24`), ou vérifier l'IP attribuée.

#### Montage de l'ISO et installation de FOG

- [ ] **3.1** — Mettre à jour le système :
  ```bash
  sudo apt update && sudo apt upgrade -y
  ```
- [ ] **3.2** — Télécharger et lancer le script officiel FOG :
  ```bash
  git clone https://github.com/FOGProject/fogproject.git /tmp/fog
  cd /tmp/fog/bin
  sudo bash installfog.sh
  ```
- [ ] **3.3** — Lors de l'installation interactive :
  - Type d'installation : **Normal Server**
  - Interface réseau : saisir le nom relevé à l'étape 2.3 (ex. `enp0s3`)
  - FOG gère-t-il le DHCP ? → **N** (réponse `N` = mode **proxyDHCP**, pas de conflit avec le DHCP de la salle)
  - Confirmer les autres paramètres par défaut.

#### Mise sous tension du serveur — vérification

- [ ] **4.1** — À la fin du script, FOG affiche l'URL de l'interface web → noter l'IP.
- [ ] **4.2** — Ouvrir un navigateur sur le PC hôte et accéder à `http://<IP-FOG>/fog/management`.
- [ ] **4.3** — Se connecter (identifiants par défaut : `fog` / `password`) puis **changer immédiatement le mot de passe**.
- [ ] **4.4** — Vérifier les services actifs dans la VM :
  ```bash
  sudo systemctl status fog*
  ```

#### Paramétrage du DHCP / PXE

- [ ] **5.1** — FOG en mode proxyDHCP n'a pas besoin d'un rôle DHCP dédié. Vérifier que le DHCP de la salle distribue bien une IP aux VMs clientes.
- [ ] **5.2** — Le service TFTP de FOG est actif par défaut — c'est lui qui sert le bootloader PXE aux clients.
- [ ] **5.3** — Configurer les VMs clientes avec **boot Réseau en 1er** dans l'ordre d'amorçage VirtualBox.

#### Enregistrement des hôtes et images dans FOG

- [ ] **6.1** — Relever l'adresse MAC de chaque VM cliente (VirtualBox → Configuration → Réseau → Avancé).
- [ ] **6.2** — Dans FOG : **Hosts → Create New Host** → saisir le nom et la MAC de chaque client.
- [ ] **6.3** — Créer une image : **Images → Create New Image** :
  - *Image Name* : `Win10-Pro-22H2`
  - *OS* : Windows 10 — *Type* : Single Disk - Resizable — *Partition* : Everything
- [ ] **6.4** — Associer l'image à l'hôte master (onglet **Image** de l'hôte).

#### Installation d'une VM cliente de test — démarrage PXE

- [ ] **7.1** — Créer une VM cliente vide (disque ≥ 40 Go, réseau identique à la VM FOG, boot Réseau en 1er).
- [ ] **7.2** — Démarrer la VM → elle contacte FOG via PXE → menu FOG s'affiche.
- [ ] **7.3** — Déclencher la tâche depuis l'interface FOG (**Basic Tasks → Deploy** ou **Capture** selon l'étape).

### Déroulement du démarrage PXE

1. La VM cliente démarre — l'ordre de boot place **Réseau en 1er**.
2. La carte réseau virtuelle envoie une requête DHCP sur le réseau.
3. Le DHCP de la salle attribue une IP ; FOG (proxyDHCP) répond avec l'adresse du serveur TFTP.
4. La VM télécharge le bootloader PXE (`undionly.kpxe`) via TFTP.
5. Le menu FOG s'affiche (fond bleu/noir, options de démarrage).
6. Si une tâche (capture ou déploiement) est en attente pour cette MAC, FOG l'exécute automatiquement via **partclone**.
7. La barre de progression s'affiche pendant le transfert d'image (10–30 min selon la taille).
8. À la fin, la VM redémarre sur son disque local.

### Difficultés rencontrées et solutions

| Problème | Cause | Solution |
|---|---|---|
| VMs 64 bits refusent de démarrer dans VirtualBox | Hyper-V actif monopolise VT-x | `bcdedit /set hypervisorlaunchtype off` en admin + redémarrage sur les 3 PC |
| Script FOG rejette le nom d'interface réseau | Ubuntu utilise `enp0s3` (nommage systemd) au lieu de `eth0` | Identifier le nom réel avec `ip a` avant de lancer le script |
| Pas de menu PXE au démarrage client | Mode réseau NAT (VM isolée) ou ordre de boot incorrect | Mettre Réseau en 1er dans l'ordre d'amorçage + vérifier mode réseau (pas NAT isolé) |
| FOG ne déclenche pas la tâche | Adresse MAC enregistrée différente de celle de la VM | Recopier l'adresse MAC exacte depuis VirtualBox → Configuration → Réseau → Avancé |

### Les captures d'écran

Les preuves photographiques sont stockées dans `10_Documentation/07_Preuves/` :
- Preuve A : bureau Windows 10 installé avec logiciels
- Preuve C : fenêtre Sysprep avec les bons réglages
- Preuve D : hôte + image créés + tâche Capture en file d'attente
- Preuve E : écran de progression FOG + image avec sa taille

### Les risques

| # | Risque | Impact | Probabilité | Mesure préventive |
|---|---|---|---|---|
| 1 | Image corrompue à la capture ou au déploiement | Élevé | Moyenne | Vérification d'intégrité après capture, test sur poste pilote |
| 2 | VM Master rallumée après Sysprep (image inutilisable) | Élevé | Faible | Snapshot `avant-sysprep` obligatoire ; capturer immédiatement après l'arrêt |
| 3 | Conflit DHCP entre FOG et le réseau de la salle | Moyen | Élevée si non géré | FOG installé en **proxyDHCP** (réponse `N`) — maîtrisé dès S2 |
| 4 | Saturation espace disque sur la VM FOG | Moyen | Faible | ~50 Go alloués à la VM FOG, vérifier avec `df -h` avant capture |
| 5 | Hyper-V réactivé si PC réinitialisé | Élevé | Moyenne | Re-vérifier avec `bcdedit` en début de séance si PC réinitialisé |

### Les vérifications

- [ ] Hyper-V désactivé sur tous les postes
- [ ] VMs FOG, master et clientes sur le **même mode réseau** VirtualBox
- [ ] Interface web FOG accessible depuis au moins 2 postes
- [ ] Services FOG actifs (`systemctl status fog*`)
- [ ] Mot de passe admin FOG changé
- [ ] Ordre de boot réseau en 1er sur les VMs clientes
- [ ] Snapshot `avant-sysprep` pris avant Sysprep
- [ ] Image FOG affiche une taille non nulle après capture

### Le rollback éventuel

| Situation | Rollback |
|---|---|
| Problème après Sysprep | Restaurer le snapshot `avant-sysprep` dans VirtualBox (clic droit → Restaurer) |
| Serveur FOG corrompu ou inaccessible | Restaurer la VM FOG depuis l'export `.ova` (Fichier → Importer un appareil virtuel) |
| Image déployée ne boote pas | Vérifier cohérence BIOS legacy + type d'image *Single Disk - Resizable* ; relancer un déploiement |

---

# Modèle de checklist

## Checklist de préparation — Projet FOG

### Prérequis matériel
- [ ] Les 3 postes sont présents et fonctionnels
- [ ] Droits administrateur disponibles sur chaque poste
- [ ] Hyper-V désactivé (si PC réinitialisé : `bcdedit /set hypervisorlaunchtype off` + redémarrage)
- [ ] Switch 8 ports et câbles RJ45 disponibles (pour démo physique)

### Machine virtuelle configurée
- [ ] VM FOG démarrée (`RenanVM` sur Ubuntu 26.04 LTS)
- [ ] Interface web FOG accessible : `http://192.168.50.10/fog/management`
- [ ] Services FOG actifs (`systemctl status fog*`)
- [ ] Mot de passe admin FOG changé (plus la valeur par défaut)

### Configuration de l'adresse IP du serveur
- [ ] IP du serveur FOG fixée à `192.168.50.10` (ou IP vérifiée avec `ip a`)
- [ ] Toutes les VMs (FOG + master + clients) sur le même réseau VirtualBox (Bridged ou Internal Network — **pas NAT isolé**)
- [ ] FOG en mode **proxyDHCP** (pas de conflit avec le DHCP de la salle)

### Images de démarrage d'installation
- [ ] ISO Windows 10 Pro 22H2 disponible sur le disque hôte
- [ ] VM `Win10-Master` créée (40 Go, réseau OK, boot Réseau en 1er, BIOS legacy)
- [ ] Snapshot `avant-sysprep` pris avant toute exécution de Sysprep
- [ ] Adresse MAC du master relevée et notée

### Rôle DHCP configuré
- [ ] FOG installé en **proxyDHCP** (réponse `N` lors de l'installation du script)
- [ ] Le DHCP de la salle distribue correctement les IP aux VMs clientes

### Options PXE configurées
- [ ] Service TFTP de FOG actif (vérifié via `systemctl status fog-tftp`)
- [ ] Ordre de boot des VMs clientes : **Réseau en 1er**, puis disque dur
- [ ] Mode réseau des VMs clientes identique à celui de la VM FOG

### Installation d'une VM cliente de test
- [ ] VM cliente vide créée (disque ≥ 40 Go, même mode réseau que FOG)
- [ ] MAC de la VM cliente enregistrée dans FOG (Hosts → Create New Host)
- [ ] Image `Win10-Pro-22H2` associée à l'hôte client
- [ ] Boot PXE CLIENT A : validé ✅
- [ ] Boot PXE CLIENT B : à tester en S3 ⏳

---

# Modèle de rapport d'incident

## Incident #1

**Num :** 001
**Date :** 11/06/2026 (Séance 2)

**Problème observé :**
Les VMs 64 bits (Ubuntu Server) refusent de démarrer dans VirtualBox. Message d'erreur : « VT-x is disabled in the BIOS for all CPU modes ».

**Impact :**
Blocage total de la séance 2. Impossibilité de créer ou démarrer toute VM 64 bits, donc impossible d'installer la VM Ubuntu qui hébergera le serveur FOG.

**Cause :**
Hyper-V (l'hyperviseur Microsoft intégré à Windows 11) est actif par défaut sur les postes de la salle. Hyper-V monopolise la virtualisation matérielle (VT-x/AMD-V), empêchant VirtualBox d'y accéder.

**Solution :**
Exécuter la commande suivante en **administrateur** sur les 3 PC, puis redémarrer :
```cmd
bcdedit /set hypervisorlaunchtype off
```
Vérifier que VirtualBox peut démarrer une VM 64 bits après le redémarrage.

**Validation :** Résolu ✅ — VMs 64 bits fonctionnelles sur les 3 postes depuis S2.

---

## Incident #2

**Num :** 002
**Date :** 11/06/2026 (Séance 2)

**Problème observé :**
Le script d'installation de FOG refuse le nom d'interface réseau saisi et abandonne l'installation.

**Impact :**
Interruption de l'installation du serveur FOG. Nécessite un redémarrage du script.

**Cause :**
Ubuntu utilise le schéma de nommage **systemd-udev** (`enp0s3`, `ens3`, etc.) au lieu du nom classique `eth0`. La documentation initiale mentionnait `eth0`, ce qui a induit en erreur lors de la saisie dans le script.

**Solution :**
Avant de lancer le script FOG, identifier le nom réel de l'interface réseau :
```bash
ip a
```
Repérer la ligne correspondant à l'interface connectée (ex. `enp0s3`) et la saisir dans le script à la place de `eth0`.

**Validation :** Résolu ✅ — installation FOG réussie avec `enp0s3`.

---

## Incident #3

**Num :** 003
**Date :** 11/06/2026 (Séance 2)

**Problème observé :**
Blocage organisationnel : Ernest réquisitionné comme auditeur par la chargée du cours, Nicolas absent. Renan a travaillé seul. La documentation était stockée uniquement en local sur le PC d'Ernest, inaccessible aux autres.

**Impact :**
Risque de perte complète du travail de S1 en cas de panne du PC d'Ernest. Impossibilité pour les autres membres de consulter ou de compléter la documentation pendant la séance.

**Cause :**
Documentation non centralisée — absence de dépôt partagé. Dépendance à un seul PC et une seule personne.

**Solution :**
Mise en place du dépôt Git + GitHub `projet-ghost-fog` en S3 avec Renan et Nicolas comme collaborateurs. Règle adoptée : `git push` en fin de chaque séance, `git pull` en début de chaque séance.

**Validation :** Résolu ✅ — dépôt GitHub opérationnel depuis S3.

---

# Modèle de journal des changements

## Journal des changements — Projet Ghost / FOG

| Num | Modifications | Auteurs | Date | Justification |
|---|---|---|---|---|
| 001 | Choix de FOG Project comme outil de déploiement ; abandon des alternatives (WDS, Clonezilla) | Ernest, Renan, Nicolas | S1 | FOG est open source, adapté au contexte étudiant, bien documenté et déjà utilisé en milieu éducatif — meilleure adéquation avec les ressources du groupe |
| 002 | Standardisation de l'hyperviseur sur **VirtualBox** pour les 3 membres | Ernest, Renan, Nicolas | S1 | Facilite l'échange d'images VDI entre membres et évite les incompatibilités |
| 003 | Désactivation de Hyper-V sur les 3 PC (`bcdedit /set hypervisorlaunchtype off`) | Renan | 11/06/2026 (S2) | Hyper-V monopolise VT-x et empêche VirtualBox de démarrer des VMs 64 bits |
| 004 | Installation FOG en mode **proxyDHCP** (réponse `N`) au lieu de DHCP complet | Renan | 11/06/2026 (S2) | Évite les conflits avec le DHCP déjà présent dans la salle de cours |
| 005 | Réorganisation complète de l'arborescence du projet (convention numérotée `00_` → `90_`) | Ernest | 18/06/2026 (S3) | Arborescence précédente non structurée, difficile à naviguer et à maintenir |
| 006 | Migration de la documentation de format PDF vers **Markdown** dans un dépôt Git | Ernest | 18/06/2026 (S3) | Le PDF ne permet pas la modification collaborative ; Markdown + Git permet push/pull et historique |
| 007 | Création du dépôt GitHub `projet-ghost-fog` avec accès collaboratif (Renan, Nicolas) | Ernest | 18/06/2026 (S3) | Réponse au risque avéré de S2 : documentation non partagée, dépendance à un seul membre |
| 008 | Correction de la version Ubuntu documentée (`22.04` → `26.04 LTS` réelle) | Ernest | 18/06/2026 (S3) | Incohérence entre la documentation initiale et la VM réellement installée en S2 |
| 009 | Rédaction du `Guide_Installation_VM_Windows10.md` | Renan | 18/06/2026 (S3) | Guide manquant pour la création des VMs clientes, nécessaire à la reproductibilité du déploiement FOG |
