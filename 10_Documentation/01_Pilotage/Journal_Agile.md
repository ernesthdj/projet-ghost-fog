# Journal de bord Agile

Document complété à chaque fin de séance.

## Séance 1

### Ce que nous voulions faire
Comprendre l'imagerie réseau, choisir l'outil de déploiement, définir une architecture cohérente
avec nos ressources, répartir les rôles et produire une première documentation professionnelle.

### Ce que nous avons réalisé
- Choix de **FOG Project** justifié (open source, adapté au contexte étudiant, bien documenté).
- Hyperviseur **VirtualBox** standardisé pour les 3 membres.
- OS cible : **Windows 10 Pro 22H2**.
- Architecture cible, schéma réseau, specs matérielles, analyse des risques, plan d'attaque rédigés.

### Ce qui a bloqué
Rien de bloquant : séance d'analyse et de cadrage.

### Ce que nous ferons à la prochaine séance
Préparer l'environnement (VirtualBox sur les 3 PC), installer le serveur FOG, premiers tests réseau.

---

## Séance 2 — 11/06/2026

### Contexte particulier
**Nico absent** et **Ernest réquisitionné comme auditeur** d'autres groupes par la chargée du cours.
Renan a donc travaillé **seul**, sur la base de la documentation préparée en séance 1.

### Ce que nous voulions faire
Désactiver Hyper-V + installer VirtualBox sur les 3 PC, créer la VM Ubuntu, installer FOG, valider
l'accès à l'interface web, réaliser un premier boot PXE.

### Ce que nous avons réalisé
- Hyper-V désactivé, VirtualBox opérationnel sur les 3 machines.
- VM **Ubuntu 26.04 LTS** (`RenanVM`) créée et installée (utilisateur `renan`, OpenSSH activé).
- **FOG Project installé** via le script officiel, en mode **proxyDHCP** (réponse `N` au DHCP).
- Interface web FOG **accessible depuis les postes**, premier hôte client enregistré.
- **Boot PXE validé sur CLIENT A.**
- Procédure d'installation v1 rédigée et testée.

### Ce qui a bloqué
- **Hyper-V** empêchait VirtualBox de démarrer les VMs 64 bits → résolu par `bcdedit /set hypervisorlaunchtype off`.
- **Nom d'interface réseau** inattendu (`enp0s3` ≠ `eth0`) lors du script FOG → résolu via `ip a`.
- **Constat majeur :** la documentation n'était pas centralisée, tout reposait sur Renan — risque de continuité.

### Ce que nous ferons à la prochaine séance
PXE CLIENT B, VM Windows Master + Sysprep, première capture d'image, export `.ova` de sauvegarde,
et surtout **centraliser la documentation** pour survivre aux absences.

---

## Séance 3 — 18/06/2026

### Contexte particulier
Séance axée sur la **continuité** : un projet doit pouvoir être repris même si un membre part.
Risque d'absence d'un ou plusieurs membres (non connu à l'avance).

### Ce que nous voulions faire
1. Vérifier que chaque membre comprend le projet (objectif, avancement, blocages, suite).
2. **Centraliser la documentation dans un dépôt GitHub** partagé (push/pull même en cas d'absence).
3. Réorganiser et uniformiser les dossiers et la documentation.
4. Rédiger le **Dossier de reprise**.
5. Poursuivre les travaux techniques (capture d'image Windows).

### Ce que nous avons réalisé
- Réorganisation complète des dossiers du projet (arborescence numérotée claire).
- Centralisation : rapatriement du travail S2 (jusque-là dans un PDF) dans le vault Markdown.
- Création du **Dossier de reprise** (`30_Dossier_Reprise/`).
- Mise en place du **dépôt Git + GitHub** `projet-ghost-fog`.
- _(à compléter en fin de séance : avancée technique du jour)_

### Ce qui a bloqué
_À compléter en fin de séance._

### Ce que nous ferons à la prochaine séance
_À compléter en fin de séance._
