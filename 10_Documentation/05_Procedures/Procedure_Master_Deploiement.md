# Procédure complète — Préparation du Master & Déploiement FOG

> **Responsable :** Ernest (déploiement master) · **Rédacteur doc :** Ernest
> **Objectif de ce document :** être suffisamment détaillé pour qu'un membre **qui n'a jamais fait
> l'opération** puisse la dérouler seul, en cas d'absence du responsable.
> **Pré-requis :** serveur FOG installé et opérationnel (fait en S2), interface web accessible.

> ⚠️ **Statut des étapes :** la phase A→B est une *procédure de référence à exécuter* (pas encore
> validée en réel). Coche `[x]` et ajoute la preuve correspondante dans `../07_Preuves/` au fur et à
> mesure. Une étape sans preuve = étape non réalisée (règle du cours).

---

## Fiche express (pour aller vite)

```
A. VM Windows Master : créer → installer Windows 10 Pro → installer logiciels
B. SNAPSHOT VirtualBox  ← FILET DE SÉCURITÉ, ne pas l'oublier
C. Sysprep : Généraliser + OOBE + Arrêt  → la VM s'éteint SEULE (ne pas la rallumer)
D. FOG : enregistrer l'hôte (MAC) → créer l'image → tâche CAPTURE
E. Démarrer la VM Master → PXE → capture automatique → image stockée sur le serveur
F. Déploiement : VM cliente vide → enregistrer (MAC) → tâche DEPLOY → PXE → Windows déployé
G. Vérifier : Windows démarre, nom + SID uniques
```

**Vocabulaire utile**
- **PXE** (*Preboot eXecution Environment*) : démarrage de la machine **depuis le réseau** au lieu du disque.
- **Sysprep** (*System Preparation Tool*) : outil Windows qui « généralise » l'image (supprime le **SID** et les identifiants uniques) pour qu'elle soit déployable sur plusieurs PC.
- **SID** (*Security Identifier*) : identifiant unique d'une installation Windows. Deux PC avec le même SID = conflits → c'est pour ça qu'on fait Sysprep.
- **Master** : la machine de référence dont on capture l'image.
- **`.ova`** : fichier d'export d'une VM complète (pour sauvegarde).

---

## Phase A — Créer et préparer la VM Windows Master

> Sur le PC qui hébergera le master. Réseau : **identique à celui de la VM FOG** (voir note réseau en bas).

- [ ] **A.1** — Créer une VM dans VirtualBox :

| Paramètre | Valeur |
|---|---|
| Nom | `Win10-Master` |
| Type / Version | Microsoft Windows — Windows 10 (64-bit) |
| RAM | 4 096 Mo (4 Go) |
| Processeurs | 2 vCPU |
| Disque | 40 Go — allocation dynamique (`.vdi`) |
| Réseau — Carte 1 | **même mode que la VM FOG** (voir note réseau) |
| Ordre de boot | **Réseau en 1er**, puis disque dur |
| Firmware | BIOS legacy (plus simple que UEFI pour FOG) |

- [ ] **A.2** — Monter l'ISO **Windows 10 Pro 22H2** et démarrer la VM.
- [ ] **A.3** — Installer Windows :
  - Édition : **Windows 10 Pro**
  - Si demandé : « Je n'ai pas de clé de produit » (image d'évaluation).
  - Partitionnement par défaut, attendre la fin et le redémarrage.
  - À l'écran OOBE : créer un **compte local** simple (ex. `master`), pas de compte Microsoft.
- [ ] **A.4** — Installer les **logiciels voulus** sur le master (ceux qu'on veut retrouver sur tous les postes déployés). Faire les mises à jour Windows souhaitées.
- [ ] **A.5** — *(Recommandé)* Faire le ménage : vider la corbeille, supprimer les fichiers temporaires (réduit la taille de l'image).

> 📸 **Preuve A** : capture du bureau Windows installé avec les logiciels → `../07_Preuves/`.

---

## Phase B — Snapshot de sécurité (NE PAS SAUTER)

- [ ] **B.1** — VM Master **éteinte proprement** (menu Démarrer → Arrêter).
- [ ] **B.2** — Dans VirtualBox : VM Master → onglet **Instantanés (Snapshots)** → **Prendre** un instantané nommé `avant-sysprep`.

> **Pourquoi c'est critique :** Sysprep « généralise » l'image et ne peut être relancé qu'un nombre
> limité de fois (compteur *rearm*). Si quelque chose tourne mal, ce snapshot permet de **revenir à
> l'état d'avant** sans tout réinstaller.

---

## Phase C — Sysprep (généralisation de l'image)

- [ ] **C.1** — Démarrer la VM Master, ouvrir l'**Explorateur de fichiers** et aller dans :
  ```
  C:\Windows\System32\Sysprep\
  ```
- [ ] **C.2** — Double-cliquer sur **`sysprep.exe`**.
- [ ] **C.3** — Régler exactement :
  - **Action de nettoyage du système** : `Entrer en mode OOBE (Out-Of-Box Experience)`
  - Cocher **`Généraliser`** ✅ (indispensable : c'est ce qui supprime le SID)
  - **Options d'arrêt** : `Arrêter le système`
- [ ] **C.4** — Cliquer **OK**. Sysprep travaille puis **éteint la VM tout seul**.

> 🚫 **RÈGLE ABSOLUE : ne PAS rallumer la VM Master après Sysprep.** Au prochain démarrage, Windows
> relancerait la phase OOBE et « re-personnaliserait » l'installation → l'image ne serait plus
> généralisée. On enchaîne directement sur la capture.

> 📸 **Preuve C** : capture de la fenêtre Sysprep avec les bons réglages → `../07_Preuves/`.

---

## Phase D — Préparer la capture dans FOG

> Depuis n'importe quel PC : ouvrir l'interface web `http://<IP-serveur-FOG>/fog/management`.

- [ ] **D.1** — Relever l'**adresse MAC** de la VM Master :
  VirtualBox → VM Master → Configuration → Réseau → Avancé → *Adresse MAC*. **La noter.**
- [ ] **D.2** — Dans FOG : menu **Hosts → Create New Host** :
  - *Host Name* : `Win10-Master`
  - *Primary MAC* : l'adresse relevée en D.1
  - Enregistrer (*Add*).
- [ ] **D.3** — Créer l'image : menu **Images → Create New Image** :
  - *Image Name* : `Win10-Pro-22H2`
  - *Operating System* : `Windows 10`
  - *Image Type* : **Single Disk - Resizable** (gère le redimensionnement de la partition Windows)
  - *Partition* : `Everything` (tout le disque)
  - Enregistrer.
- [ ] **D.4** — Associer l'image à l'hôte : **Hosts → Win10-Master → onglet *Image*** → choisir `Win10-Pro-22H2` → *Update*.
- [ ] **D.5** — Lancer la tâche de capture : **Hosts → Win10-Master → Basic Tasks → `Capture`**.
  La tâche passe « en attente » : FOG attend que la machine démarre en réseau.

> 📸 **Preuve D** : capture de l'hôte créé + image associée + tâche Capture en file d'attente.

---

## Phase E — Capturer l'image (PXE)

- [ ] **E.1** — Démarrer la VM Master. Comme l'ordre de boot est « Réseau d'abord », elle démarre en **PXE** et contacte le serveur FOG.
- [ ] **E.2** — FOG détecte la tâche Capture et lance **partclone** : il lit le disque et envoie l'image vers le serveur. Une barre de progression s'affiche.
- [ ] **E.3** — Attendre la fin (selon la taille, 10–30 min). La VM s'éteint / redémarre à la fin.
- [ ] **E.4** — Vérifier dans FOG : **Images → Win10-Pro-22H2** → la taille de l'image est renseignée → la capture a réussi.

> 📸 **Preuve E** : capture de l'écran de progression FOG + de l'image présente avec sa taille.

> 💾 **B.bis — Sauvegarde serveur :** une fois l'image capturée, exporter la **VM FOG en `.ova`**
> (VirtualBox → VM FOG → Fichier → Exporter un appareil virtuel) avant toute autre manipulation.

---

## Phase F — Déployer l'image sur une VM cliente

> Sur le PC client (Renan ou Nicolas), même réseau que FOG.

- [ ] **F.1** — Créer une **VM cliente vide** :
  - Disque **≥ 40 Go** (au moins la taille du master), allocation dynamique.
  - Réseau : même mode que FOG. Ordre de boot : **Réseau en 1er**.
- [ ] **F.2** — Relever sa **MAC** et l'enregistrer dans FOG (**Hosts → Create New Host**, ex. `Client-A`).
- [ ] **F.3** — Associer l'image `Win10-Pro-22H2` à cet hôte (onglet *Image*).
- [ ] **F.4** — Lancer la tâche : **Hosts → Client-A → Basic Tasks → `Deploy`**.
- [ ] **F.5** — Démarrer la VM cliente → PXE → FOG **écrit l'image** sur le disque (partclone).
- [ ] **F.6** — À la fin, la VM redémarre sur le disque local : Windows lance une **mini-installation (OOBE)** parce que l'image est généralisée → choix de la région, création du compte, etc.

> 📸 **Preuve F** : capture du déploiement en cours + du Windows déployé qui démarre.

---

## Phase G — Vérification (Responsable Qualité : Nicolas)

- [ ] **G.1** — Windows démarre correctement sur la VM cliente.
- [ ] **G.2** — Les logiciels installés sur le master sont bien présents.
- [ ] **G.3** — Vérifier que le **SID est unique** (preuve que Sysprep a fonctionné) :
  ```cmd
  whoami /user
  ```
  (le SID affiché doit être différent de celui du master).
- [ ] **G.4** — Le **nom de la machine** est unique sur le réseau.

> 📸 **Preuve G** : capture du `whoami /user` sur le client déployé.

---

## ⚠️ Note réseau (point à confirmer avant la capture)

Le mode réseau de la VM FOG doit être **cohérent entre le serveur, le master et les clients**, sinon
le PXE ne fonctionne pas entre machines.

- En **NAT** (IP type `10.0.2.15`) : la VM n'est joignable que depuis son propre hôte → la
  capture/déploiement ne marche **qu'entre VMs d'un même PC**.
- En **Bridged** ou **Réseau interne (Internal Network)** partagé : les VMs se voient entre elles → PXE inter-machines possible.

👉 **Avant la capture**, vérifier dans VirtualBox (Configuration → Réseau de chaque VM) que toutes
les VMs impliquées sont sur le **même réseau**, et que FOG est bien en **proxyDHCP** (réponse `N` au
DHCP à l'installation). Voir [Architecture_Cible](../03_Architecture/Architecture_Cible.md).

---

## En cas de problème

| Symptôme | Piste |
|---|---|
| La VM ne boote pas en réseau (pas de menu FOG) | Vérifier l'ordre de boot (Réseau en 1er) + le mode réseau (pas NAT isolé) |
| FOG ne lance pas la tâche | Vérifier que la **MAC** enregistrée = celle de la VM |
| Windows redemande l'activation OOBE sur le master | Le master a été rallumé après Sysprep → revenir au snapshot `avant-sysprep` |
| Image déployée mais Windows ne boote pas | Vérifier BIOS legacy des deux côtés + type d'image *Single Disk - Resizable* |

> Tout incident rencontré doit être consigné dans
> [`../04_Risques_Checklists/Journal_Incidents.md`](../04_Risques_Checklists/Journal_Incidents.md).
