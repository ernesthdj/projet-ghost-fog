# Rapport de séance 2

## Informations générales

| Champ | Valeur |
|---|---|
| Date | 11/06/2026 |
| Numéro de séance | 02 |
| Membres présents | Demeereleere Renan (seul) |
| Membres absents | Hadjiu Ernest (réquisitionné comme auditeur), Terroir Nicolas (absent) |
| Thème | Installation & premiers tests |

> Synthèse rapatriée depuis le livrable PDF de Renan : [`../../20_Livrables/Seance_02/`](../../20_Livrables/Seance_02/)

## Objectifs de la séance

Désactiver Hyper-V + installer VirtualBox sur les 3 PC, créer la VM Ubuntu, installer FOG Project,
valider l'accès à l'interface web, réaliser un premier boot PXE.

## Travail réalisé

- Hyper-V désactivé, VirtualBox opérationnel sur les 3 machines.
- VM **Ubuntu 26.04 LTS** (`RenanVM`) créée et installée (utilisateur `renan`, OpenSSH).
- **FOG Project installé** via le script officiel, en mode **proxyDHCP** (réponse `N` au DHCP).
- Interface web FOG **accessible depuis les postes** de la salle.
- Premier hôte client enregistré dans FOG.
- **Boot PXE validé sur CLIENT A.**
- Procédure d'installation v1 rédigée et testée.

## Difficultés rencontrées

- **Hyper-V** empêchait VirtualBox de démarrer les VMs 64 bits.
- **Nom d'interface réseau** inattendu : `enp0s3` au lieu de `eth0`.

## Solutions

- `bcdedit /set hypervisorlaunchtype off` (admin) + redémarrage — appliqué sur les 3 PC.
- Identification du nom réel via `ip a` avant de relancer le script FOG.

## Tests réalisés

| Test | Résultat | Statut |
|---|---|---|
| Connexion VM Ubuntu | Connexion réussie, infos système affichées | ✅ OK |
| Accès internet (`ping 8.8.8.8`) | `apt update` exécuté sans erreur | ✅ OK |
| Installation FOG via script | URL d'accès affichée | ✅ OK |
| Interface web FOG (CLIENT A et B) | Page de connexion accessible | ✅ OK |
| Services FOG (`systemctl status fog*`) | Services actifs | ✅ OK |
| Boot PXE CLIENT A | Menu FOG affiché | ✅ OK |
| Boot PXE CLIENT B | Non testé | ⏳ Reporté S3 |
| Capture image Windows | VM Master pas encore préparée | ❌ Reporté S3 |

## Travail restant pour la séance suivante

PXE CLIENT B · VM Windows Master + Sysprep · première capture d'image · export `.ova` de la VM FOG ·
**centralisation de la documentation** (constat clé de la séance).

## Bilan

Séance 2 réussie malgré le travail en solo : serveur FOG installé, opérationnel et accessible, PXE
validé sur CLIENT A. Constat majeur : la dépendance à une seule personne et la documentation non
centralisée → priorité S3 = dépôt Git partagé.
