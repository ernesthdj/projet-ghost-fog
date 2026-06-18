# Plan d'attaque

## Objectif final du projet

Déployer une image Windows 10 Pro 22H2 via un serveur FOG, vers des VMs clientes, en réseau.
Produire une documentation complète permettant à un autre groupe de reproduire le déploiement à
partir des seuls livrables remis.

## Étapes prévues

| Étape | Description | Séance prévue | État |
|---|---|---|---|
| 1 | Analyse et cadrage du projet | S1 | ✅ Terminé |
| 2 | Choix de l'outil et justification (FOG) | S1 | ✅ Terminé |
| 3 | Définition de l'architecture cible | S1 | ✅ Terminé |
| 4 | Documentation initiale et plan de travail | S1 | ✅ Terminé |
| 5 | Désactivation Hyper-V + VirtualBox sur les 3 PC | S2 | ✅ Terminé |
| 6 | Création VM Ubuntu + installation du serveur FOG | S2 | ✅ Terminé |
| 7 | Accès interface web FOG + 1er hôte + PXE CLIENT A | S2 | ✅ Terminé |
| 8 | Centralisation doc + dépôt Git + dossier de reprise | S3 | 🔄 En cours |
| 9 | PXE CLIENT B (reporté de S2) | S3 | ⏳ À faire |
| 10 | VM Windows Master + logiciels + Sysprep | S3 | ⏳ À faire |
| 11 | Enregistrement master FOG + capture de l'image | S3 | ⏳ À faire |
| 12 | Export `.ova` de la VM FOG (sauvegarde) | S3 | ⏳ À faire |
| 13 | Création VMs clientes + déploiement de l'image | S4 | ⏳ À faire |
| 14 | Tests post-déploiement et validation | S4-S5 | ⏳ À faire |
| 15 | Procédure de restauration en cas d'incident | S5 | ⏳ À faire |
| 16 | Documentation finale et préparation défense orale | S6 | ⏳ À faire |

## Priorités immédiates — Séance 3

1. **Centraliser la doc** dans le dépôt GitHub partagé (continuité en cas d'absence) — *en cours*.
2. Tester le **boot PXE sur CLIENT B** (Nico).
3. Créer et préparer la **VM Windows Master + Sysprep** (Renan).
4. Lancer la **première capture d'image** Windows via FOG (Ernest).
5. **Exporter la VM FOG en `.ova`** avant toute manipulation risquée.
