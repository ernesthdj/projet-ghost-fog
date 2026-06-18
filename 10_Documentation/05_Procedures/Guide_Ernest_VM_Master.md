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
- [x] L'**ISO Windows 10 Pro 22H2** (le fichier `.iso`) quelque part sur ton disque
- [ ] Le **serveur FOG démarré** (la VM Ubuntu) et son interface web qui s'ouvre dans le navigateur
- [ ] L'**adresse IP du serveur FOG** sous la main (tu la verras dans la VM Ubuntu avec la commande `ip a`, c'est la ligne `enp0s3`)
- [ ] Au moins **40 Go de libre** sur ton disque

> 💡 Si l'interface FOG ne s'ouvre pas, règle ça **avant** de continuer (rien ne marchera sans le serveur).

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
- **Réseau → Carte 1** : choisis **le même mode que ta VM FOG**. ⚠️ Voir l'encadré réseau plus bas — c'est LE point qui peut tout bloquer.
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
2. Ouvre l'interface FOG : `http://<IP-du-serveur-FOG>/fog/management`, connecte-toi.
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

## ⚠️ Encadré réseau (lis-le avant l'étape 1)

Pour que FOG « parle » à la VM Master, **les deux doivent être sur le même réseau VirtualBox**.
- **NAT** (IP qui commence par `10.0.2.x`) : la VM est isolée → ❌ la capture ne marchera **que** si FOG et master sont sur le **même PC**, et même là c'est compliqué.
- **Réseau interne (Internal Network)** avec le même nom des deux côtés, OU **Bridged** : ✅ les VMs se voient.

👉 Avant de lancer, ouvre la config réseau de **la VM FOG** et de **la VM Master** : elles doivent
avoir le **même mode**. Si tu as un doute, demande-toi : « le master tourne-t-il sur le même PC que
FOG, ou sur un autre ? » et règle le réseau en conséquence. *(Ce point n'est pas encore tranché dans
notre doc — c'est noté dans le Dossier de reprise §8.)*

---

## 🆘 Si ça coince

| Tu vois… | Fais ça |
|---|---|
| Pas de menu FOG au démarrage (la VM boote sur Windows ou « no bootable medium ») | Vérifie l'ordre de boot (Réseau en 1er) **et** le mode réseau (pas NAT isolé) |
| FOG ne démarre pas la capture | La MAC enregistrée dans FOG ≠ celle de la VM → recopie-la correctement |
| Windows relance sa config sur le master | Tu l'as rallumé après Sysprep → restaure le snapshot `avant-sysprep` et refais Sysprep |
| Tu paniques | Respire 🙂 tu as un snapshot `avant-sysprep` : tu ne peux rien casser de définitif |

> 📝 Note **tout** incident dans [`../04_Risques_Checklists/Journal_Incidents.md`](../04_Risques_Checklists/Journal_Incidents.md) — c'est un livrable noté de la séance.
