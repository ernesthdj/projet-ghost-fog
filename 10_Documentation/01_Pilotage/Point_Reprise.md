# 🔖 Point de reprise — où on en est

> Dernière session : **25-26/06/2026** · Responsable déploiement master : **Ernest**
> But du document : reprendre exactement là où on s'est arrêté, sans rien chercher.
> Guide détaillé associé : [`../05_Procedures/Guide_Ernest_VM_Master.md`](../05_Procedures/Guide_Ernest_VM_Master.md)

---

## ✅ Ce qui est FAIT et fonctionnel

### Serveur FOG (VM `FOG-Server`) — 100 % opérationnel
- VM Ubuntu Server `fog-server`, **FOG 1.5.10** installé, « Setup complete! ».
- Réseau : **Host-only** « VirtualBox Host-Only Ethernet Adapter » (192.168.56.x).
  - Carte 1 = **NAT** (`enp0s3`, Internet) · Carte 2 = **Host-only** (`enp0s8` = **192.168.56.10**, fixe).
  - **FOG est le serveur DHCP** du segment (DHCP de VirtualBox désactivé). Routeur/passerelle : 192.168.56.1.
- Interface web : **http://192.168.56.10/fog/management** — login `fog`, **mot de passe changé** (noté par Ernest).
- Base de données installée, services FOG démarrés.

### VM Master (`Win10_master`) — Windows en cours d'installation
- VM créée : Windows 10 (64-bit), 4 Go RAM, 2 vCPU, disque 40 Go, ISO `Windows.iso` montée.
- **Windows 10 Pro** installé **sans clé** (« Je n'ai pas de clé de produit »), installation **Personnalisée**.
- **Arrêt de session : fin de l'installation Windows** (avant/pendant la config OOBE — région/compte).

---

## ▶️ À REPRENDRE À LA PROCHAINE SESSION (dans l'ordre)

### 0. Rallumer le serveur (si PC éteint entre-temps)
- [ ] Démarrer la VM **`FOG-Server`** et la **laisser allumée** (FOG doit tourner pour la capture).
- [ ] Vérifier l'accès web : `http://192.168.56.10/fog/management`.

### 1. Finir l'installation Windows (OOBE) — ÉTAPE 2 du guide
- [ ] Région **France**, clavier **Français**, ignorer le 2ᵉ clavier.
- [ ] **Compte LOCAL** : « Je n'ai pas Internet » → « Continuer avec l'installation limitée »
      (si bloqué sur compte Microsoft : `Shift+F10` → `OOBE\BYPASSNRO` → Entrée).
- [ ] Utilisateur **`master`**, mot de passe vide → confidentialité tout sur Non → **bureau Windows**.

### 2. Installer les logiciels du master — ÉTAPE 3
- [ ] Installer les logiciels voulus (à retrouver sur tous les postes déployés), faire le ménage.
- [ ] 📸 Capture du bureau → `../07_Preuves/`.

### 3. Snapshot de sécurité — ÉTAPE 4 (NE PAS SAUTER)
- [ ] VM éteinte → VirtualBox → `Win10_master` → Instantanés → **Prendre** → nom **`avant-sysprep`**.

### 4. Sysprep — ÉTAPE 5
- [ ] `C:\Windows\System32\Sysprep\sysprep.exe` → OOBE + **Généraliser** + **Arrêter**.
- [ ] 🚫 **Ne PAS rallumer** la VM après Sysprep. 📸 Capture des réglages Sysprep.

### 5. Capture via FOG — ÉTAPES 6 et 7
- [ ] **Régler le réseau de `Win10_master`** sur **Host-only** « VirtualBox Host-Only Ethernet Adapter »
      (⚠️ à vérifier — il a peut-être été laissé en NAT par défaut pendant l'install).
- [ ] **Ordre d'amorçage** : remettre **Réseau en premier** (était CD pour l'install).
- [ ] Relever la **MAC** du master → FOG : Create Host (`Win10-Master`) → Create Image
      (`Win10-Pro-22H2`, Single Disk - Resizable) → associer → tâche **Capture**.
- [ ] Démarrer le master → PXE → capture → vérifier l'image et sa taille dans FOG.
- [ ] 📸 Captures (hôte/image/tâche + progression + image finale). Puis **export `.ova`** de la VM FOG.

---

## ⚠️ Points d'attention / à vérifier
- Réseau du master : **à confirmer en Host-only** avant la capture (sinon pas de PXE).
- Encadré jaune « File Not Found » (Storage Node Disk Usage) dans le dashboard FOG : **cosmétique** sur install neuve — vérifier que le stockage `/images` fonctionne avant/pendant la 1ʳᵉ capture.
- Déploiement multi-PC (vers Renan/Nico, autres machines physiques) : nécessitera le **switch physique isolé** prévu en S1 — étape ultérieure.

## 🗂️ Rappel des 2 VM
| VM | Rôle | À la reprise |
|---|---|---|
| `FOG-Server` | Serveur FOG (Ubuntu) | À **rallumer** et laisser tourner |
| `Win10_master` | Windows à préparer/capturer | Reprendre la config OOBE |
