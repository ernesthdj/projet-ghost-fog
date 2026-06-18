# Guide pas-à-pas — Installation d'une VM Windows 10 Pro sur VirtualBox

> 👋 **Pour qui :** tout membre de l'équipe devant monter un poste Windows 10 virtuel (client FOG, poste de test…).
> 🎯 **Ce que tu vas accomplir :** créer et configurer une machine virtuelle Windows 10 Pro fonctionnelle sous VirtualBox, prête à être utilisée ou intégrée dans la chaîne FOG.
> ⏱️ **Durée réaliste :** ~45 min à 1h15 (l'installation Windows est longue mais passive).
>
> 📖 Ce guide est volontairement détaillé. La version courte et formelle (liste de tâches à cocher) est dans [`Procedure_Master_Deploiement.md`](Procedure_Master_Deploiement.md) — Phase A.

---

## 🧠 D'abord, comprendre en 30 secondes

VirtualBox est un **hyperviseur** : il crée des ordinateurs virtuels (VM) qui tournent dans une fenêtre de ton PC. Chaque VM a son propre disque, sa propre RAM, sa propre carte réseau — comme un vrai PC, en moins cher.

On installe Windows 10 Pro dans une VM pour :
- Préparer un **master FOG** (image à déployer sur d'autres machines).
- Tester un déploiement FOG sans risquer son vrai système.
- Avoir un environnement Windows isolé et reproductible.

---

## ✅ Avant de commencer — vérifie que tu as tout

- [ ] **VirtualBox** installé (version 6.1 ou supérieure)
- [ ] L'**ISO Windows 10 Pro** (fichier `.iso`) sur ton disque — ex. `Win10_22H2_French_x64.iso`
- [ ] Au moins **50 Go de libre** sur ton disque hôte
- [ ] Au moins **8 Go de RAM** sur ton PC (pour laisser 4 Go à la VM)

> 💡 L'ISO Windows 10 se télécharge officiellement sur le site Microsoft via l'outil *Media Creation Tool*. Si tu n'as pas de clé de produit, Windows s'installe en mode évaluation (fonctionnel, avec un filigrane).

---

## ÉTAPE 1 — Créer la VM dans VirtualBox

1. Ouvre **VirtualBox** → clique sur **`Nouvelle`** (icône bleue en haut à gauche).
2. Dans l'assistant, renseigne :
   - **Nom** : `Win10-Pro` (ou `Win10-Master` si c'est pour FOG)
   - **Dossier** : laisse par défaut
   - **Type** : `Microsoft Windows`
   - **Version** : `Windows 10 (64-bit)`
   - Si VirtualBox propose l'installation non assistée (*Unattended*), coche **« Skip Unattended Installation »** — on veut piloter l'installation nous-mêmes.
3. Clique **Suivant**.

### Configuration matérielle

| Paramètre | Valeur recommandée | Minimum |
|---|---|---|
| **RAM** | 4 096 Mo (4 Go) | 2 048 Mo |
| **Processeurs** | 2 vCPU | 1 vCPU |
| **Disque dur** | 50 Go — allocation dynamique (`.vdi`) | 40 Go |

4. Pour la RAM, déplace le curseur ou saisis **`4096`** Mo → **Suivant**.
5. Pour le disque : sélectionne **« Créer un disque dur virtuel maintenant »** → **VDI** → **Allocation dynamique** → taille **50 Go** → **Créer**.

> 💡 **Allocation dynamique** : le fichier `.vdi` ne prend sur ton disque que l'espace réellement utilisé par la VM — pas 50 Go d'un coup.

---

## ÉTAPE 2 — Réglages importants avant le premier démarrage

Avant de lancer la VM, il faut affiner la configuration. Sélectionne `Win10-Pro` → clique sur **Configuration** (icône en engrenage).

### Système

- **Carte mère** : ordre d'amorçage → assure-toi que **Disque dur** est en premier (ou **Réseau** en premier si c'est pour FOG). Coche uniquement ce dont tu as besoin.
- **Carte mère** → désactive le **disque de disquette** s'il est coché.
- **Processeur** : passe à **2 vCPU** si tu ne l'as pas encore fait.
- **Accélération** : laisse « Activer VT-x/AMD-V » coché.

### Affichage

- **Mémoire vidéo** : monte à **128 Mo**.
- Active l'**Accélération 3D** si ton PC le supporte (améliore les performances graphiques).

### Réseau

| Cas | Mode réseau à choisir |
|---|---|
| Accès internet simple depuis la VM | **NAT** (valeur par défaut) |
| VM devant communiquer avec d'autres VMs (ex. VM FOG) sur le **même PC** | **Réseau interne** (même nom des deux côtés) |
| VM devant être vue sur le réseau local physique | **Accès par pont (Bridged)** |

> ⚠️ Si cette VM est destinée à être capturée par FOG, choisis le **même mode réseau que la VM FOG** — c'est indispensable pour que le PXE fonctionne. Voir [`../03_Architecture/Architecture_Cible.md`](../03_Architecture/Architecture_Cible.md).

### Stockage — monter l'ISO

1. Dans **Stockage**, clique sur le petit icône de CD marqué **« Vide »**.
2. À droite, clique sur l'icône de disque → **« Choisir un fichier de disque »**.
3. Navigue jusqu'à ton fichier **ISO Windows 10** et sélectionne-le.
4. Clique **OK** pour fermer la configuration.

> 📸 **Capture à prendre** : la fenêtre de configuration VirtualBox avec l'ISO monté et les paramètres réseau. À déposer dans `../07_Preuves/`.

---

## ÉTAPE 3 — Installer Windows 10 Pro

1. Sélectionne `Win10-Pro` → clique **Démarrer**.
2. La VM démarre sur l'ISO. *(Si un message « Press any key to boot from CD » apparaît, clique dans la fenêtre de la VM et appuie rapidement sur une touche.)*

### Déroulé de l'installation

- [ ] **3.1** — Sélectionne la langue **Français (France)**, le format horaire **Français** et le clavier **Français** → **Suivant** → **Installer maintenant**.
- [ ] **3.2** — Clé de produit → clique **« Je n'ai pas de clé de produit »** (activation ultérieure possible).
- [ ] **3.3** — Choisis l'édition **Windows 10 Pro** → **Suivant**.
- [ ] **3.4** — Accepte les termes du contrat de licence → **Suivant**.
- [ ] **3.5** — Type d'installation → **Personnalisé : installer uniquement Windows (avancé)**.
- [ ] **3.6** — Sélectionne le disque de 50 Go → **Suivant**. *(Windows crée automatiquement les partitions système — c'est normal.)*
- [ ] **3.7** — ⏳ L'installation commence. Windows **copie les fichiers et redémarre plusieurs fois** — c'est normal, ne touche à rien pendant cette phase (~15-25 min).

> 💡 C'est le bon moment pour faire une pause — la VM gère tout seule cette phase.

### Configuration initiale (OOBE)

Après les redémarrages, Windows lance son assistant de configuration :

- [ ] **3.8** — Région : **France** → **Oui**.
- [ ] **3.9** — Clavier : **Français** → **Oui** → **Ignorer** (pas de 2e disposition).
- [ ] **3.10** — Connexion au réseau : clique **« Je n'ai pas d'internet »** puis **« Continuer avec une configuration limitée »** — pour créer un **compte local** (pas de compte Microsoft).
- [ ] **3.11** — Nom d'utilisateur : saisis `master` (ou le nom voulu) → **Suivant**.
- [ ] **3.12** — Mot de passe : tu peux laisser **vide** pour un lab, ou mettre un mot de passe simple.
- [ ] **3.13** — Questions de sécurité : clique **Ignorer** si le compte n'a pas de mot de passe.
- [ ] **3.14** — Options de confidentialité : **refuse tout** (mets tous les curseurs sur Non) → **Accepter**.
- [ ] **3.15** — Options d'assistant Cortana : clique **Pas maintenant**.

Tu arrives sur le **bureau Windows 10 Pro**. 🎉

> 📸 **Capture à prendre** : le bureau Windows installé avec la barre des tâches visible.

---

## ÉTAPE 4 — Installer les Additions Invité VirtualBox (recommandé)

Les *Additions Invité* améliorent considérablement le confort d'utilisation : résolution auto, copier-coller, dossiers partagés, meilleures performances graphiques.

1. Dans la fenêtre VirtualBox, menu **Périphériques → Insérer l'image CD des Additions invité…**
2. Dans la VM, ouvre l'**Explorateur de fichiers** → lecteur CD → double-clique sur **`VBoxWindowsAdditions.exe`**.
3. Suis l'assistant → **Installer** → **Redémarrer maintenant**.

> 💡 Après le redémarrage, tu peux redimensionner la fenêtre VirtualBox et la VM s'adapte automatiquement.

---

## ÉTAPE 5 — Post-installation et logiciels

### Mises à jour Windows

- [ ] **5.1** — Menu Démarrer → **Paramètres** → **Mise à jour et sécurité → Windows Update** → **Rechercher les mises à jour**. Installe-les et redémarre si nécessaire.

> ⚠️ Ne pas faire les mises à jour si cette VM est un **master FOG à capturer bientôt** — les mises à jour alourdiront l'image. Décide d'abord la liste finale des logiciels.

### Logiciels à installer

Installe ici tout ce que tu veux retrouver sur le poste (ou sur toutes les machines si c'est un master FOG) :

- [ ] Navigateur (Firefox, Chrome…)
- [ ] Suite bureautique (LibreOffice…)
- [ ] Outils spécifiques au projet

> 🧹 **Avant de capturer (si master FOG) :** vide la Corbeille, supprime les fichiers temporaires (`%temp%` dans la barre d'adresse de l'Explorateur) → l'image sera plus légère.

> 📸 **Capture à prendre** : le bureau avec les logiciels installés.

---

## ÉTAPE 6 — Snapshot de sécurité (conseil fortement recommandé)

Un snapshot te permet de revenir à un état précis si quelque chose se passe mal ensuite (Sysprep, test d'un logiciel, etc.).

1. **Éteins proprement** la VM (menu Démarrer → Arrêter).
2. Dans VirtualBox : sélectionne `Win10-Pro` → icône **≡ → Instantanés (Snapshots)**.
3. Clique **Prendre**, nomme l'instantané `propre-post-install`, valide.

> 💡 Si tu vas ensuite faire Sysprep pour FOG, renomme ce snapshot `avant-sysprep`. Voir [`Guide_Ernest_VM_Master.md`](Guide_Ernest_VM_Master.md) pour la suite.

---

## 🆘 Dépannage — problèmes courants

| Tu vois… | Fais ça |
|---|---|
| Fenêtre noire au démarrage de la VM | Clique dans la fenêtre VM et appuie sur une touche. Si rien, vérifie que l'ISO est bien montée dans **Stockage** |
| Message « This PC can't run Windows 11 » | Vérifie que la **version** dans VirtualBox est bien `Windows 10 (64-bit)` et non Windows 11 |
| La résolution est bloquée à 800×600 | Installe les **Additions Invité** (étape 4) |
| Pas d'accès internet dans la VM | Le mode NAT devrait fonctionner par défaut — vérife que la **Carte 1** est activée dans Configuration → Réseau |
| L'installation boucle sur un redémarrage | Retire l'ISO après le premier redémarrage : **Périphériques → Lecteurs optiques → Retirer le disque** |
| VirtualBox signale « VT-x/AMD-V désactivé » | Active la virtualisation dans le BIOS de ton PC hôte (option `Intel VT-x` ou `AMD SVM`) |

> 📝 Note tout incident dans [`../04_Risques_Checklists/Journal_Incidents.md`](../04_Risques_Checklists/Journal_Incidents.md).

---

## ➡️ Et après ?

| Objectif | Prochaine étape |
|---|---|
| Utiliser cette VM comme **master FOG** | Aller à l'**Étape 4** de [`Guide_Ernest_VM_Master.md`](Guide_Ernest_VM_Master.md) (Snapshot + Sysprep + Capture) |
| Utiliser cette VM comme **client FOG** (déploiement) | Aller à la **Phase F** de [`Procedure_Master_Deploiement.md`](Procedure_Master_Deploiement.md) |
| Utiliser cette VM comme **poste de travail isolé** | C'est prêt — bon travail 🙂 |
