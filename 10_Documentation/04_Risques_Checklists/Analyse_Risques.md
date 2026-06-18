# Analyse des risques

> Mise à jour S2/S3 : la colonne **Statut** suit l'évolution réelle des risques au fil des séances.

## Tableau principal

| # | Risque | Impact | Probabilité | Statut | Mesure préventive / Retour d'expérience |
|---|---|---|---|---|---|
| 1 | Image système corrompue à la capture ou au déploiement | Élevé | Moyenne | Non rencontré | Vérification d'intégrité après capture, test sur poste pilote avant déploiement de masse |
| 2 | Perte de données lors d'un redéploiement | Élevé | Moyenne | Non rencontré | Sauvegarde préalable imposée, double validation avant lancement |
| 3 | Conflit entre DHCP FOG et DHCP existant | Moyen | Élevée si non géré | **Maîtrisé (S2)** | FOG installé en **proxyDHCP** (réponse `N`) — pas de conflit avec le DHCP de la salle |
| 4 | Procédure ambiguë / non reproductible par un tiers | Élevé | Moyenne | À surveiller | Relecture croisée (Qualité), validation par exécution réelle, **doc centralisée sur Git** |
| 5 | Absence d'un membre lors d'une séance | Moyen | **Avérée (S2)** | **Maîtrisé (S3)** | **Dépôt Git partagé** : chacun peut push/pull même seul. Journal Agile à jour |
| 6 | Incompatibilité de format de VM | Faible | Faible | Maîtrisé | Standardisation VirtualBox actée |
| 7 | Indisponibilité du switch ou des câbles | Élevé (démo) | Faible | Non rencontré | Vérif matériel via checklist ; démo physique facultative |
| 8 | Droits administrateur insuffisants | Moyen | Faible | Maîtrisé | Comptes admin vérifiés sur les 3 postes (S2) |
| 9 | Saturation de l'espace disque à la capture | Moyen | Faible | Maîtrisé | ~25 Go alloués à la VM FOG, espace confirmé (`df -h`) |
| 10 | Perte de la documentation (panne d'un portable) | Élevé | Faible | **Maîtrisé (S3)** | **Dépôt Git distant** + synchro OneDrive |
| 11 | Faille de sécurité sur la VM FOG (mdp par défaut) | Moyen | Moyenne | Maîtrisé | Mot de passe admin FOG changé à la 1re connexion |
| 12 | Sysprep mal exécuté (SID dupliqués) | Moyen | Moyenne | Non rencontré | Procédure Sysprep documentée — à appliquer strictement en S3 |
| 13 | PXE boot non fonctionnel sur une VM cliente | Élevé | Moyenne | **Partiel (S2)** | Validé CLIENT A ; **CLIENT B à tester en S3** |
| 14 | Hyper-V bloquant VirtualBox (VMs 64 bits) | Élevé | Élevée | **Résolu (S2)** | `bcdedit /set hypervisorlaunchtype off` + redémarrage, à revérifier si PC réinitialisé |
| 15 | Nom d'interface réseau Ubuntu inattendu | Moyen | Moyenne | **Résolu (S2)** | Toujours vérifier avec `ip a` avant le script FOG (`enp0s3`) |
| 16 | VM Master redémarrée après Sysprep (image inutilisable) | Élevé | Faible | Non rencontré | Capturer immédiatement après l'arrêt Sysprep ; snapshot avant |

## Niveau de criticité global

- **Critiques (impact élevé) :** 1, 2, 4, 13 — attention renforcée dans les procédures à produire.
- **Moyens :** 3, 5, 11, 12 — mesures préventives systématiques (plusieurs déjà maîtrisés).
- **Faibles / maîtrisés :** 6, 7, 8, 9, 10, 14, 15 — couverts par les mesures actées.

## Risques organisationnels (mise en avant S3)

Le risque le plus concret rencontré n'est pas technique mais **organisationnel** : l'absence simultanée
de membres (avérée en S2) combinée à une documentation non centralisée. La réponse retenue en S3 est
la **mise en place d'un dépôt Git partagé**, qui transforme ce risque de « bloquant » à « maîtrisé ».
