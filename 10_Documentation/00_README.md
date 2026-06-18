# Documentation — Projet Ghost Server

Espace de documentation Markdown (vault Obsidian) pour le projet Ghost Server, BAC Info IPAMC, 2025-2026.

> 📍 Ce dossier (`10_Documentation/`) est **la source unique de vérité** du projet.
> Pour la vue d'ensemble du dépôt, voir le [`README.md`](../README.md) à la racine.
> Pour reprendre le projet de zéro, voir le [Dossier de reprise](../30_Dossier_Reprise/Dossier_Reprise.md).

## Membres du groupe

| Membre | Rôle officiel |
|---|---|
| Hadjiu Ernest | Coordinateur, Responsable Documentation |
| Demeereleere Renan | Technicien Infrastructure |
| Terroir Nicolas | Responsable Qualité et Tests |

## Décisions techniques verrouillées

| Domaine | Choix |
|---|---|
| Outil de déploiement | FOG Project |
| Hyperviseur | VirtualBox (tous les 3) |
| OS de la VM serveur | Ubuntu 26.04 LTS *(la planif S1 visait 22.04 ; réalité S2 : 26.04)* |
| OS cible à déployer | Windows 10 Pro 22H2 |
| Service DHCP | FOG en **proxyDHCP** (ne distribue pas d'IP, cohabite avec le DHCP de la salle) |
| Démo physique | Optionnelle |
| Mode de preuve | Captures, exports, journaux |

## Organisation du vault

| Dossier | Contenu |
|---|---|
| `01_Pilotage` | Rapports de séance, plan d'attaque, journal Agile, répartition |
| `02_Analyse` | Rapport d'analyse, présentation FOG, déploiement d'image, cas d'usage, réflexions ITIL |
| `03_Architecture` | Architecture cible, schéma réseau, specs matérielles |
| `04_Risques_Checklists` | Analyse des risques, checklist de préparation |
| `05_Procedures` | Procédures techniques (workflow FOG, installation…) |
| `06_Sources` | Documentation externe collectée |
| `07_Preuves` | Captures, exports, fichiers de configuration |
| `99_Templates` | Gabarits réutilisables |

## Règles d'usage

1. Toute information non documentée n'existe pas.
2. Chaque action technique produit au moins une preuve (capture ou export) dans `07_Preuves`.
3. Le `Journal_Agile.md` est complété à chaque fin de séance.
4. Le `Rapport_Seance_XX.md` est créé en début de séance et clôturé en fin.
5. Les sources externes utilisées sont consignées dans `06_Sources/Documentation_Collectee.md`.
6. **Travail en équipe : `git pull` avant de commencer, `git push` après.**

## Statut

Séance 3 (18/06/2026) en cours — centralisation de la documentation et poursuite technique.
Serveur FOG opérationnel depuis la S2, boot PXE validé sur CLIENT A.
