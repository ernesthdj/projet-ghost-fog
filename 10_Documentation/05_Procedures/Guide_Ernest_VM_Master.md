# Guide pas-à-pas — Déploiement de la VM Master (Ernest)

> 👋 **Pour qui :** Ernest, responsable du déploiement master — **première fois**.
> 🎯 **Ce que tu vas accomplir :** créer une machine Windows 10 « modèle » (le *master*), la nettoyer
> avec Sysprep, puis la capturer dans FOG pour pouvoir la déployer ensuite sur d'autres machines.
> ⏱️ **Durée réaliste :** ~1h30 à 2h (l'installation de Windows et la capture sont longues mais passives).
>
> 📖 Ce guide est volontairement bavard et rassurant. La version courte et formelle (pour la doc
> d'équipe) est dans [`Procedure_Master_Deploiement.md`](Procedure_Master_Deploiement.md).

---

## 🧠 D'abord, comprendre en 30 secondes

On veut éviter de réinstaller Windows à la main sur chaque PC. Donc :
1. On installe **une seule fois** un Windows « parfait » avec les bons logiciels → c'est le **master**.
2. On le « généralise » avec **Sysprep** (on enlève son identité unique, le **SID**, pour qu'il
   puisse être copié sur plusieurs machines sans conflit).
3. **FOG** prend une **photo du disque** (la *capture* / image) et la garde sur le serveur.
4. Plus tard, FOG **recopie** cette image sur d'autres machines (le *déploiement*).

Ton job aujourd'hui = les étapes **1, 2 et 3**.

---

## ✅ Avant de commencer — vérifie que tu as tout

- [x] **VirtualBox** ouvert sur ton PC
- [x] L'**ISO Windows 10 Pro 22H2** (le fichier `.iso`) sur ton disque
- [x] L'**ISO Ubuntu Server** (pour le serveur FOG) — à télécharger à l'ÉTAPE 0
- [x] **Le serveur FOG** : il n'existe pas encore sur ton PC → c'est **l'ÉTAPE 0 ci-dessous** qui le crée
- [x] Au moins **~120 Go de libre** (≈ 80 Go pour le serveur FOG + ses images, + 40 Go pour le master)

> 💡 **Rappel décision séance 1 :** c'est **toi (Ernest) qui héberges la VM serveur FOG**. En séance 2,
> Renan en avait monté une sur **sa** machine en ton absence ; ici tu montes **la tienne**, sur ton PC.
> Tout va donc tourner sur une seule machine : on commence par installer le serveur (ÉTAPE 0), puis le master.

---

## ÉTAPE 0 — Mettre en place le serveur FOG (sur ton PC)

> 🎯 Objectif : avoir une VM Ubuntu qui fait tourner FOG, accessible depuis ton navigateur.
> ⏱️ ~45 min (dont installation Ubuntu, en grande partie passive).

### 🧭 Le réseau qu'on va monter (et pourquoi)

La séance 1 a décidé : **segment réseau isolé**, et **FOG est le serveur DHCP/PXE** de ce segment.
La S1 prévoyait l'adressage `192.168.56.x` tout en précisant qu'il « peut être ajusté lors de
l'installation effective ». On garde donc le réseau Host-only par défaut de VirtualBox en
**`192.168.56.x`** (intervalle autorisé sans config supplémentaire) : IP fixe FOG **192.168.56.10**,
passerelle **192.168.56.1**, plage clients **192.168.56.100 → .200**.

Comme tu es sur **une seule machine** (pas le switch physique à 3 portables de la S1), on reproduit ce
« segment isolé » avec un **réseau Hôte privé (Host-Only)** de VirtualBox nommé `fognet`. C'est
l'équivalent du switch isolé : les VMs s'y voient, ton PC peut y accéder (pour ouvrir l'interface
FOG), et **aucun autre DHCP** n'y traîne → FOG peut être le DHCP **sans conflit**, exactement comme
décidé en S1.

> 📝 Note de cohérence : en **S2 à l'école**, Renan a répondu **N** à la question DHCP (mode proxyDHCP)
> parce que la salle avait **déjà** un DHCP. Toi, sur ton segment **isolé**, tu réponds **Y** : FOG est
> le seul DHCP, donc il doit l'assurer. Même logique « pas de conflit DHCP », appliquée à ton réseau.

### 0.1 — Télécharger Ubuntu Server
Sur [ubuntu.com/download/server](https://ubuntu.com/download/server), télécharge l'ISO
**Ubuntu Server 26.04 LTS** *(la version utilisée par l'équipe en S2, pour rester aligné avec le serveur de Renan)*.

### 0.2 — Le réseau isolé (déjà prêt ✅)
Pas besoin de le créer : VirtualBox a déjà un réseau Host-only utilisable, et il a été configuré pour toi :
- Adaptateur **« VirtualBox Host-Only Ethernet Adapter »**, IP **192.168.56.1** (= la passerelle, ton PC sur le segment).
- **Serveur DHCP de VirtualBox désactivé** dessus → c'est bien **FOG** qui fera le DHCP (décision S1).

> Tu n'as donc rien à faire ici. Quand tu configureras les cartes réseau des VMs (étapes suivantes),
> tu choisiras simplement ce même adaptateur **« VirtualBox Host-Only Ethernet Adapter »**.
> *(Si un jour tu dois le vérifier toi-même : c'est dans le ≡ menu « Outils » du panneau de gauche → Réseau.)*

### 0.3 — Créer la VM serveur FOG
Sélectionne **Nouvelle** → renseigne (specs décidées en S1) :

| Paramètre | Valeur |
|---|---|
| Nom | `FOG-Server` |
| Type / Version | Linux — Ubuntu (64-bit) |
| RAM | 8192 Mo (minimum 2048) |
| Processeurs | 2 |
| Disque | **80 Go** dynamique (stockage des images) |
| Firmware | BIOS legacy |

Puis **Configuration → Réseau**, monte **deux cartes** :
- **Carte 1** : **NAT** → sert juste à donner Internet à la VM pendant l'installation (téléchargements).
- **Carte 2** : **Réseau Hôte privé (Host-only)** → adaptateur **« VirtualBox Host-Only Ethernet Adapter »**.
  C'est le segment isolé sur lequel FOG fera le DHCP/PXE.

Dans **Stockage**, monte l'ISO Ubuntu Server dans le lecteur optique.

> 💡 Pourquoi 2 cartes ? La NAT = Internet pour installer. La Host-only `fognet` = le segment isolé du
> PXE. On sépare les deux, dans l'esprit « segment isolé » de la S1.

### 0.4 — Installer Ubuntu Server
Démarre `FOG-Server`. L'installateur enchaîne **plusieurs écrans** (c'est normal qu'il y en ait beaucoup).
Navigation : **flèches** pour bouger, **Espace** pour cocher, **Entrée** pour valider (la souris ne marche pas).

| Écran | Quoi faire |
|---|---|
| Langue | English (ou Français) |
| (éventuel) Mise à jour de l'installateur | « Continue without updating » |
| Disposition clavier | French si clavier AZERTY |
| Type d'installation | « Ubuntu Server » (par défaut), **pas** « minimized » |
| Connexions réseau | Laisser tel quel. La carte NAT (`enp0s3`) prend une IP auto ; la carte host-only (`enp0s8`) reste **sans IP** → **normal**, on la fixe en 0.5 → **Terminé** |
| **Proxy** | Laisser **vide** → **Terminé** |
| Miroir d'archive | Laisser par défaut → **Terminé** |
| Stockage guidé | « Use an entire disk » → **Terminé** |
| Résumé du stockage | ⚠️ Par défaut, Ubuntu (LVM) n'alloue que **~50 %** du disque à `/`. Pour récupérer tout l'espace (objectif 80 Go S1) : aller sur **`ubuntu-lv`** (mounted at /) → **Edit** → champ **Size** = le **max** (≈ `79G`) → **Save**. Puis **Terminé** → **Continue** (confirme le formatage) |
| Profil | Nom · serveur : `fog-server` · utilisateur : **`fog`** · mot de passe (⚠️ **note-les**) |
| Ubuntu Pro | « Skip for now » |
| SSH Setup | ⚠️ **Cocher « Install OpenSSH server »** (Espace) → **Terminé** |
| Featured snaps | N'en cocher **aucun** → **Terminé** |
| Installation | Attendre (~10 min) → **« Reboot Now »** + **retirer l'ISO** → se connecter |

### 0.5 — Donner une IP fixe au segment isolé
Sur un réseau isolé, il n'y a pas de DHCP au départ → le serveur a besoin d'une IP fixe.
1. Liste les interfaces : `ip a`
   - `enp0s3` = carte **NAT** (a déjà une IP en `10.0.2.x`, Internet) → **ne pas toucher**.
   - `enp0s8` = carte **Host-only fognet** (sans IP pour l'instant) → c'est elle qu'on fixe.
2. Édite le fichier réseau : `sudo nano /etc/netplan/50-cloud-init.yaml`
   *(s'il porte un autre nom, fais `ls /etc/netplan/` et édite celui qui s'y trouve)*
3. Mets ceci (⚠️ indentation = 2 espaces, pas de tabulation) :
   ```yaml
   network:
     version: 2
     ethernets:
       enp0s3:
         dhcp4: true
       enp0s8:
         dhcp4: false
         addresses: [192.168.56.10/24]
   ```
4. Applique : `sudo netplan apply`
5. Vérifie : `ip a` → `enp0s8` doit afficher **192.168.56.10**.

### 0.6 — Installer FOG Project
Commandes (validées par l'équipe en S2) :
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git
git clone https://github.com/FOGProject/fogproject.git /opt/fogproject
cd /opt/fogproject/bin
sudo bash installfog.sh
```
Réponses au script (cohérentes avec la S1) :

| Question du script | Réponse | Pourquoi |
|---|---|---|
| Type d'installation | `N` (Normal Server) | serveur complet |
| Interface réseau | **`enp0s8`** | l'interface du segment isolé `fognet` (**surtout pas** `enp0s3` qui est la NAT) |
| Ce serveur est-il le serveur DHCP ? | **`Y` (Oui)** | **décision S1** : FOG = DHCP du segment isolé (aucun autre DHCP → aucun conflit) |
| Adresse IP du serveur | `192.168.56.10` | IP fixe S1 |
| Passerelle (routeur) | `192.168.56.1` | passerelle S1 (= ton adaptateur Host-only) |
| Plage DHCP à distribuer | `192.168.56.100` → `192.168.56.200` | pool clients S1 |
| Serveur DNS | laisser défaut (ou `8.8.8.8`) | |
| Langue / autres | Entrée (valeurs par défaut) | |

⏳ Le script télécharge et configure (MySQL, Apache, TFTP…). À la fin, il affiche une **URL**.

### 0.7 — Finaliser via l'interface web
1. Sur **ton PC hôte**, ouvre le navigateur : **`http://192.168.56.10/fog/management`**
   *(ton PC accède au segment via l'adaptateur Host-only 192.168.56.1)*.
2. Clique **Install/Update Database** au premier accès.
3. Connecte-toi : `fog` / `password`.
4. **Change immédiatement le mot de passe** (menu Users) — et note-le dans la doc.
5. Vérifie que le tableau de bord FOG s'affiche.

> 📸 **Captures à prendre** : le script FOG terminé (URL affichée) + le tableau de bord FOG connecté.
> 🎉 Ton serveur FOG est prêt. Laisse la VM `FOG-Server` **allumée** pendant toute la suite.

---

## ÉTAPE 1 — Créer la VM Master dans VirtualBox

1. Dans VirtualBox, clique sur **`Nouvelle`** (icône bleue en étoile).
2. Renseigne :
   - **Nom** : `Win10-Master`
   - **Type** : `Microsoft Windows`
   - **Version** : `Windows 10 (64-bit)`
   - Coche éventuellement « Skip Unattended Installation » si VirtualBox te le propose (on veut installer Windows nous-mêmes, à la main).
3. **Mémoire vive** : mets le curseur sur **4096 Mo** (4 Go).
4. **Processeurs** : **2** (onglet Système → Processeur si besoin).
5. **Disque dur** : « Créer un disque dur virtuel maintenant » → **VDI** → **Allocation dynamique** → **40 Go**.
6. Clique **Créer**.

### Réglages importants à faire AVANT de démarrer
Sélectionne `Win10-Master` → **Configuration** :
- **Système → onglet Carte mère** : ordre d'amorçage → mets **Réseau (Network)** tout en haut de la liste, puis **Disque dur**. *(Coche/décoche avec les flèches.)* C'est ce qui permettra le boot PXE plus tard.
- **Réseau → Carte 1** : **Réseau Hôte privé (Host-only)** → réseau **`fognet`** (le même que le serveur FOG). ⚠️ Une seule carte suffit pour le master. C'est LE point qui peut tout bloquer s'il est faux.
- **Stockage** : clique sur le petit CD « Vide » → à droite, icône disque → **Choisir un fichier de disque** → sélectionne ton **ISO Windows 10**.

> 📸 **Capture à prendre** (preuve S3) : la fenêtre de config réseau de la VM. À déposer dans `../07_Preuves/`.

---

## ÉTAPE 2 — Installer Windows 10

1. Sélectionne `Win10-Master` → **Démarrer**.
2. La VM boote sur l'ISO. *(Si un message « Press any key to boot from CD » apparaît, clique dans la fenêtre et appuie sur une touche rapidement.)*
3. Déroule l'installation :
   - Langue / clavier → **Français** → Suivant → **Installer maintenant**.
   - Clé de produit → **« Je n'ai pas de clé de produit »**.
   - Édition → **Windows 10 Pro**.
   - Accepter les conditions.
   - Type d'installation → **Personnalisé**.
   - Disque → sélectionne le disque de 40 Go → **Suivant** (Windows crée les partitions tout seul).
4. ⏳ Windows s'installe et **redémarre plusieurs fois tout seul** — c'est normal, ne touche à rien.
5. À la configuration (OOBE) :
   - Région **France**, clavier **Français**.
   - Choisis **« Options de connexion » → Compte hors connexion / Compte local**.
   - Nom d'utilisateur simple : `master`. Mot de passe : tu peux laisser vide pour un lab.
   - Refuse les options de confidentialité superflues (tout sur Non/Refuser).
6. Tu arrives sur le **bureau Windows**. 🎉

> 💡 C'est l'étape la plus longue (~15-25 min). Tu peux faire autre chose pendant l'install.

---

## ÉTAPE 3 — Installer les logiciels du master

Installe ici **tout ce que tu veux retrouver sur chaque machine déployée** (navigateur, suite bureautique, etc.) + les mises à jour Windows souhaitées.

> 🧹 Astuce : à la fin, vide la corbeille et supprime les fichiers temporaires → l'image sera plus légère et la capture plus rapide.

> 📸 **Capture à prendre** : le bureau avec les logiciels installés.

---

## ÉTAPE 4 — 🛟 SNAPSHOT de sécurité (ne saute JAMAIS cette étape)

1. **Éteins proprement** Windows (menu Démarrer → Arrêter).
2. Dans VirtualBox : sélectionne `Win10-Master` → en haut à droite, menu **≡ → Instantanés** (Snapshots).
3. Clique **Prendre**, nomme-le **`avant-sysprep`**, valide.

> **Pourquoi c'est vital :** Sysprep (étape suivante) ne peut être lancé qu'un nombre limité de fois,
> et si ça se passe mal, c'est irréversible. Ce snapshot = ton bouton « annuler ». Si quoi que ce soit
> foire après, clic droit sur `avant-sysprep` → **Restaurer**, et tu repars de zéro sans réinstaller.

---

## ÉTAPE 5 — Sysprep (généraliser l'image)

1. Démarre `Win10-Master`, ouvre l'**Explorateur de fichiers**, va dans :
   ```
   C:\Windows\System32\Sysprep\
   ```
2. Double-clique **`sysprep.exe`**.
3. Règle **exactement** ces 3 champs :
   - **Action de nettoyage du système** : `Entrer en mode OOBE (Out-Of-Box Experience)`
   - ☑️ **Coche « Généraliser »** ← le plus important
   - **Options d'arrêt** : `Arrêter le système`
4. Clique **OK**. Sysprep travaille quelques minutes, puis **la VM s'éteint toute seule**.

> 🚫 **RÈGLE D'OR : ne rallume PAS la VM Master après Sysprep.** Si tu la redémarres, Windows
> recommence sa configuration et l'image n'est plus « neutre ». On passe directement à la capture.

> 📸 **Capture à prendre** : la fenêtre Sysprep avec tes réglages, **avant** de cliquer OK.

---

## ÉTAPE 6 — Préparer la capture dans FOG (interface web)

1. Note l'**adresse MAC** de la VM Master : VirtualBox → `Win10-Master` → Configuration → Réseau → Avancé → **Adresse MAC** (12 caractères). Recopie-la.
2. Ouvre l'interface FOG : `http://192.168.56.10/fog/management`, connecte-toi.
3. **Hosts → Create New Host** :
   - *Host Name* : `Win10-Master`
   - *Primary MAC* : colle l'adresse MAC notée
   - **Add**.
4. **Images → Create New Image** :
   - *Image Name* : `Win10-Pro-22H2`
   - *Operating System* : `Windows 10`
   - *Image Type* : **Single Disk - Resizable**
   - *Partition* : `Everything`
   - **Add**.
5. **Hosts → Win10-Master → onglet Image** : sélectionne `Win10-Pro-22H2` → **Update**.
6. **Hosts → Win10-Master → Basic Tasks → `Capture`**. La tâche se met en file d'attente (FOG attend la machine).

> 📸 **Capture à prendre** : l'hôte créé + l'image associée + la tâche Capture en attente.

---

## ÉTAPE 7 — Lancer la capture

1. Démarre la VM `Win10-Master`. Comme le boot réseau est en premier, elle démarre en **PXE** et contacte FOG.
2. Un écran FOG (fond bleu/noir, texte) apparaît, puis **partclone** affiche une barre de progression : FOG lit le disque et envoie l'image au serveur.
3. ⏳ Attends la fin (10-30 min selon la taille). La VM s'éteindra / redémarrera à la fin.
4. Vérifie dans FOG : **Images → Win10-Pro-22H2** → une **taille** est affichée → ✅ capture réussie !

> 📸 **Capture à prendre** : l'écran de progression + l'image finale avec sa taille.

> 💾 **Juste après :** exporte la **VM FOG en `.ova`** (VirtualBox → VM FOG → Fichier → *Exporter un appareil virtuel*) pour avoir une sauvegarde du serveur avec l'image dedans.

---

## 🎉 Et voilà — c'est fait

Tu as un master capturé dans FOG. La suite (déployer sur les VMs clientes) est l'étape de Renan/Nicolas,
décrite en **Phase F** de [`Procedure_Master_Deploiement.md`](Procedure_Master_Deploiement.md).

---

## ⚠️ Encadré réseau — récap du montage retenu

Tout repose sur **un seul réseau** : le Host-only **`fognet`** (= le « segment isolé » de la S1, version mono-PC).

| Élément | Réseau | IP |
|---|---|---|
| Ton PC hôte (= passerelle) | « VirtualBox Host-Only Ethernet Adapter » | `192.168.56.1` |
| VM `FOG-Server` | Carte 1 NAT (Internet) **+** Carte 2 Host-only | `192.168.56.10` (fixe, sur `enp0s8`) |
| VM `Win10-Master` | Host-only (1 carte) | reçue via le DHCP de FOG (`192.168.56.100+`) |

Règles d'or :
- **VirtualBox DHCP désactivé** sur `fognet` → c'est **FOG** le DHCP (décision S1).
- Le **master n'a PAS de carte NAT** : il doit booter en PXE sur `fognet`, pas chercher Internet.
- Si le master ne reçoit pas d'IP / pas de menu FOG → vérifie qu'il est bien sur `fognet` et que la VM `FOG-Server` est allumée.

---

## 🆘 Si ça coince

| Tu vois… | Fais ça |
|---|---|
| Pas de menu FOG au démarrage (la VM boote sur Windows ou « no bootable medium ») | Vérifie l'ordre de boot (Réseau en 1er) **et** le mode réseau (pas NAT isolé) |
| FOG ne démarre pas la capture | La MAC enregistrée dans FOG ≠ celle de la VM → recopie-la correctement |
| Windows relance sa config sur le master | Tu l'as rallumé après Sysprep → restaure le snapshot `avant-sysprep` et refais Sysprep |
| Tu paniques | Respire 🙂 tu as un snapshot `avant-sysprep` : tu ne peux rien casser de définitif |

> 📝 Note **tout** incident dans [`../04_Risques_Checklists/Journal_Incidents.md`](../04_Risques_Checklists/Journal_Incidents.md) — c'est un livrable noté de la séance.
