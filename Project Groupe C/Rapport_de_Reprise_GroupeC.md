# Rapport de reprise — Projet « Serveur Ghost » du Groupe C

> **Séance 4 — Phase 2 : Transfert du projet**
> Exercice : notre équipe reçoit le projet d'un autre groupe et doit le reprendre **uniquement sur base
> des documents transmis**, sans pouvoir interroger l'équipe d'origine dans un premier temps.

| Champ | Valeur |
|---|---|
| Équipe repreneuse | Ernest Hadjiu · Renan Demeereleere · Nicolas Terroir |
| Projet repris | « Déploiement serveur Ghost » |
| Équipe d'origine | Théo C · Théo W · Mamadou |
| Documents reçus | `Projet groupe deploiement serveur Ghost.pdf` · `dossier_de_reprise (1).pdf` |
| Date de reprise | 25/06/2026 |

---

## Phase 3 — Analyse du dossier reçu

Légende : ✅ Compris · ⚠️ Partiellement compris · ❌ Non compris

| Élément | Compris ? | Commentaires |
|---|---|---|
| **Objectif du projet** | ✅ Oui | Clair et redondant entre les deux docs : produire une **image disque** (copie complète d'un disque) via un « serveur Ghost », pour servir de **backup** et de **modèle de clonage** vers d'autres postes. Objectif pédagogique annexe explicité (travail d'équipe : planification, préparation, validation). |
| **Architecture** | ⚠️ Partiel | Seulement un **schéma conceptuel** (Serveur Ghost ↔ réseau ↔ PC1/PC2 : PC1 envoie son image, le serveur la stocke, PC2 la récupère). **Aucune architecture concrète** : pas de plan d'adressage IP (Internet Protocol — identifiant réseau d'une machine), pas de specs des machines virtuelles (mémoire, disque), pas d'hyperviseur nommé, pas de mode réseau (NAT / Bridged / Host-only). La checklist S1 mentionne « switch dédié ou VLAN isolé » et « DHCP de déploiement » mais sans valeurs réelles. |
| **État d'avancement** | ✅ Oui | Honnête et lisible : compréhension du sujet validée, **installation du logiciel commencée (« bcp de problèmes »)**, dossier de reprise et rapport journalier rédigés. **La phase de test n'est PAS faite.** Statut global : « en cours de peaufinage ». |
| **Procédures** | ❌ Non | **Bloquant.** Les procédures d'installation et de configuration renvoient à « **voir procédure discord** » — document **non transmis**, donc inaccessible pour nous. La procédure de test est inexistante (« pas encore réalisé »). Aucune procédure reproductible n'est exploitable en l'état. |
| **Risques identifiés** | ⚠️ Partiel | Le **rapport d'analyse S1** contient un bon tableau de risques (mauvaise image, perte de données, mauvaise procédure de récupération, mauvaise config réseau, espace disque, MAJ non appliquée — avec impact + prévention). **MAIS** dans le **dossier de reprise**, la section « Analyse de risque » (risques techniques / organisationnels / mesures préventives) est **laissée vide** (titres sans contenu). Incohérence entre les deux livrables. |
| **Travail restant** | ✅ Oui | Explicitement listé : **(1)** terminer l'installation et réaliser les tests · **(2)** mettre à jour la documentation. Cohérent avec l'état d'avancement. |

### Constat de fond (avant manipulation technique)

**Ambiguïté critique sur l'outil réel.** Le rapport d'analyse décrit longuement **Symantec Ghost** (logiciel
propriétaire, avec ses CVE — Common Vulnerabilities and Exposures, failles de sécurité référencées). Mais le
dossier de reprise parle de **PXE** (Preboot eXecution Environment — démarrage d'un OS par le réseau sans
support physique) et d'installation **sur un OS serveur** (Windows Server). Or Symantec Ghost ne « s'installe »
pas vraiment ainsi. **On ne peut pas déterminer avec certitude quel logiciel est réellement déployé** ni sa
version — c'est l'angle mort principal du dossier.

---

## Phase 4 — Recherche des informations manquantes

| Information recherchée | Trouvée ? | Impact sur le projet |
|---|---|---|
| **Procédures d'installation et de configuration** (renvoyées vers Discord) | ❌ Non | **Bloquant majeur.** Sans elles, impossible de reproduire ou de poursuivre l'installation. Reconstitution obligatoire avant toute reprise → grosse perte de temps. |
| **Nom et version exacts du logiciel serveur** (Symantec Ghost ? autre outil de type FOG ? version ?) | ❌ Non | **Élevé.** Détermine toute la suite (méthode d'install, mode de capture/déploiement, compatibilité OS). Ambiguïté entre le « Symantec Ghost » de l'analyse et le « serveur PXE » du dossier de reprise. |
| **Plan d'adressage IP et configuration réseau** (IP du serveur, plage DHCP, mode réseau de la VM, passerelle) | ❌ Non | **Élevé.** Le PXE et le déploiement réseau ne peuvent pas fonctionner sans réseau maîtrisé. La mention « problème de wifi » suggère justement un réseau non stabilisé. |
| **Spécifications de l'environnement de virtualisation** (hyperviseur — VirtualBox/VMware ? RAM, disque, OS hôte) | ❌ Non | **Moyen.** Nécessaire pour recréer un environnement identique et reproduire l'incident de compatibilité. |
| **OS serveur retenu : rôle exact de « Windows Server 2019 »** (est-ce l'OS qui héberge le serveur d'images, ou l'OS client à capturer ?) | ⚠️ Déduit | **Moyen.** Le dossier indique que la solution a été trouvée « sur Windows Server 2019 » sans préciser son rôle. Hypothèse retenue : OS hôte du serveur d'images. À valider. |
| **Détail du « problème wifi »** (nature, machine concernée, résolu ou non ?) | ❌ Non | **Moyen.** Un déploiement d'image par le réseau exige une connexion **filaire fiable** ; un problème wifi non résolu peut rebloquer la phase de test. |
| **Comptes / identifiants** (admin serveur, OS) | ❌ Non | **Moyen.** Requis pour accéder à l'environnement existant s'il devait être récupéré tel quel. |

---

## Phase 5 — Reprise du projet

### 1. Prochaine étape logique identifiée

**Reconstituer une procédure d'installation/configuration fiable et reproductible, puis réaliser la première
phase de test** (capture d'une image d'un poste « cobaye » et redéploiement sur un second poste).

### 2. Justification du choix

1. Le **travail restant** annoncé par le Groupe C est sans ambiguïté : « terminer l'installation et réaliser les tests ». La continuité logique pointe donc vers la **phase de test**.
2. Mais cette phase est **inatteignable directement** : les procédures sont sur Discord (non fournies) et la phase de test n'a jamais été documentée. **On ne peut pas tester ce qu'on ne sait pas installer.**
3. La première action à valeur ajoutée est donc de **lever les bloquants documentaires** : figer l'outil, l'OS serveur et le réseau, puis réécrire une procédure exploitable. C'est exactement la leçon de continuité de l'exercice : *un projet doit être reproductible sans son auteur*.

### 3. Tâches réalisables (sur base des seuls documents reçus)

- [x] **Analyse complète** des deux documents et extraction de l'objectif, de l'architecture et du travail restant (ci-dessus).
- [x] **Cartographie des manques** (Phase 4) → liste précise des questions à poser dès le retour des auteurs (Phase 6).
- [x] **Lever l'ambiguïté outil — hypothèse de travail** : retenir un **serveur d'images réseau avec PXE** (cohérent avec la mention PXE du dossier de reprise et le schéma « serveur ↔ réseau ↔ PC »), plutôt que le Symantec Ghost propriétaire décrit en théorie dans l'analyse. À confirmer en Phase 6.
- [x] **Reconstitution d'une procédure d'installation cible** (squelette ci-dessous) à partir de la checklist de préparation S1 du Groupe C, qui est, elle, exploitable.

### 4. Procédure cible reconstituée (squelette à valider)

> Reconstruite à partir de la *Checklist de préparation* (rapport d'analyse S1, section V) et de l'incident de
> compatibilité documenté. Remplace le renvoi « voir Discord » manquant.

1. **Préparer l'hôte de virtualisation** (réseau en **switch dédié / VLAN isolé** pour ne pas perturber le réseau de l'école).
2. **Créer la VM serveur** sous **Windows Server 2019** (seul OS validé par le Groupe C ; Win10 et Win Server 2022 = échecs documentés).
3. **Fixer l'adressage** : IP statique pour le serveur + plage DHCP dédiée au déploiement (valeurs à définir — manquantes au dossier).
4. **Installer et configurer le logiciel serveur d'images** (étape à re-documenter intégralement).
5. **Préparer un poste cobaye** + **snapshot** de la VM vierge avant toute manipulation (mesure préventive S1).
6. **Phase de test** : capturer l'image du cobaye → la stocker sur le serveur → la redéployer sur un 2ᵉ poste via le réseau (PXE).
7. **Valider** (checklist qualité) puis **mettre à jour la documentation**.

### 5. Documentation des actions

Le présent fichier (`Rapport_de_Reprise_GroupeC.md`) **est** la trace de nos actions de reprise :
analyse, identification des manques, décision justifiée et procédure reconstituée. Toute action technique
ultérieure devra être journalisée à la suite.

---

## Phase 6 — Questions à poser au retour des anciens responsables

> Les auteurs redeviennent disponibles comme **personnes ressources** (ils ne réalisent pas le travail à notre place).
> Questions classées par priorité, issues directement de la Phase 4.

1. **(Bloquant)** Pouvez-vous nous transmettre la procédure Discord d'installation **et** de configuration ?
2. **(Bloquant)** Quel **logiciel exact** (et version) avez-vous installé : Symantec Ghost, FOG, ou autre ?
3. Quel est le **rôle de Windows Server 2019** : OS hôte du serveur d'images, ou OS client à imager ?
4. Quel **hyperviseur** et quelles **specs VM** (RAM, disque) avez-vous utilisés ?
5. Quel **plan d'adressage IP** (IP serveur, passerelle, plage DHCP) et quel **mode réseau** ?
6. Le **problème wifi** a-t-il été résolu ? Le déploiement se fait-il bien en filaire ?
7. Existe-t-il des **identifiants** (admin serveur/OS) à nous communiquer ?

---

## Rapport de reprise (synthèse à remettre)

**1. Ce que nous avons compris facilement**
L'**objectif** du projet (créer un serveur d'images disque pour backup + clonage de postes) et le **travail
restant** (finir l'installation, faire les tests, mettre à jour la doc). Le schéma conceptuel rend le principe
serveur ↔ réseau ↔ postes immédiatement lisible.

**2. Ce qui était bien documenté**
La partie **analyse théorique S1** (définition d'une image système, avantages/limites/risques du déploiement,
questions ITIL — Information Technology Infrastructure Library, référentiel de bonnes pratiques IT) et la
**checklist de préparation**, complète et réutilisable. Le **glossaire** du dossier de reprise est clair et
pédagogique. L'**état d'avancement** est honnête (les difficultés sont assumées).

**3. Ce qui nous a manqué**
Les **procédures réelles** (renvoyées vers Discord, donc absentes), le **nom/version exact du logiciel**, le
**plan d'adressage réseau** et les **specs de l'environnement**. La section « Analyse de risque » du dossier de
reprise est **vide**. Bref : tout le **concret reproductible** manque.

**4. Difficultés rencontrées**
Impossible de distinguer **quel outil** est réellement déployé (Symantec Ghost décrit en théorie ≠ PXE évoqué
en pratique). L'absence de procédure rend toute poursuite technique impossible sans reconstitution préalable.
Incohérence entre les risques bien remplis (S1) et la section risques vide (dossier de reprise).

**5. Temps perdu à rechercher des informations**
L'essentiel du temps a été consacré à **chercher des informations absentes** (procédures, outil, réseau)
plutôt qu'à avancer techniquement. Le renvoi « voir Discord » est la principale cause de perte de temps :
une information citée mais non jointe équivaut à une information manquante.

**6. Recommandations pour améliorer le dossier**
- **Intégrer les procédures dans le dossier lui-même** — jamais de renvoi vers un canal externe (Discord) : le dossier doit être **autoporteur**.
- **Nommer précisément l'outil et sa version**, avec la source de téléchargement et le hash de vérification.
- **Fournir le plan d'adressage IP et la config réseau** (valeurs réelles, pas seulement « VLAN isolé »).
- **Documenter les specs de l'environnement** (hyperviseur, RAM, disque, OS).
- **Remplir la section risques du dossier de reprise** (ne pas laisser de titres vides) et l'**aligner** avec le tableau de risques S1.
- **Capturer des preuves** (captures d'écran) de chaque étape réussie, surtout pour l'incident Win Server 2022 → 2019.

**7. Travail réalisé durant la séance**
Lecture et analyse des deux documents reçus · complétion des tableaux d'analyse (Phase 3) et des informations
manquantes (Phase 4) · choix justifié de la prochaine étape logique (Phase 5) · reconstitution d'un squelette
de procédure d'installation à partir de la checklist S1 · préparation de la liste de questions pour le retour
des auteurs (Phase 6) · rédaction du présent rapport de reprise.

---

### Critères d'évaluation — auto-positionnement

| Critère | Comment nous y répondons |
|---|---|
| Comprendre un projet existant | Phase 3 : chaque élément analysé et qualifié (✅/⚠️/❌). |
| Utilisation de la documentation | Appui exclusif sur les 2 documents reçus + checklist S1 réutilisée. |
| Esprit d'analyse | Détection des incohérences (outil ambigu, risques vides, renvoi Discord). |
| Qualité du rapport de reprise | Document structuré, 7 points complétés, recommandations actionnables. |
| Capacité à poursuivre le projet | Phase 5 : étape suivante identifiée + procédure cible reconstituée. |
| Communication d'équipe | Phase 6 : questions priorisées prêtes pour les personnes ressources. |
