# Projet Ghost — Serveur d'imagerie réseau FOG

> BAC Info IPAMC · Année 2025-2026 · Cours : Systèmes d'exploitation (chargée : Vanessa Robbe)
> Groupe : **Hadjiu Ernest** · **Demeereleere Renan** · **Terroir Nicolas**

Déploiement d'une image **Windows 10 Pro 22H2** via un serveur **FOG Project** (Free Open Ghost),
hébergé sur une VM **Ubuntu** dans **VirtualBox**, vers des VMs clientes — avec une documentation
suffisamment complète pour qu'une **autre équipe puisse reprendre le projet** en cas d'absence.

---

## 🎯 Pourquoi ce dépôt existe

La séance 2 a révélé le vrai risque du projet : **la dépendance à une seule personne**.
Nico était absent, Ernest a été réquisitionné comme auditeur — Renan s'est retrouvé seul avec
une documentation éparpillée sur un seul bureau. Ce dépôt Git centralise tout pour que
**chacun puisse `pull` / `push`, même en cas d'absence**.

> 👉 Si tu reprends ce projet sans nous connaître, commence par
> **[`30_Dossier_Reprise/Dossier_Reprise.md`](30_Dossier_Reprise/Dossier_Reprise.md)**.

---

## 📁 Organisation du dépôt

| Dossier | Contenu |
|---|---|
| `00_Consignes/` | Consignes officielles du cours (S1 → S3), au format PDF |
| `10_Documentation/` | **Le savoir vivant** (vault Obsidian, Markdown) — source unique de vérité |
| `20_Livrables/` | Exports figés remis à chaque séance (PDF / HTML) |
| `30_Dossier_Reprise/` | **Dossier de reprise** — point d'entrée pour un repreneur externe |
| `90_Ressources/` | Templates de mise en page, schémas, fichiers annexes |

Le dossier `10_Documentation/` est un **vault Obsidian** (config `.obsidian/` à la racine).
Tu peux le lire dans n'importe quel éditeur Markdown, ou l'ouvrir dans Obsidian pour suivre les liens `[[…]]`.

### Carte rapide de `10_Documentation/`
```
01_Pilotage/          Rapports de séance, plan d'attaque, journal Agile, répartition
02_Analyse/           Rapport d'analyse, présentation FOG, comparatif outils, cas d'usage
03_Architecture/      Architecture cible, schéma réseau, specs matérielles
04_Risques_Checklists/  Analyse des risques, checklist de préparation
05_Procedures/        Procédures techniques (workflow FOG, installation…)
06_Sources/           Documentation externe collectée
07_Preuves/           Captures d'écran et preuves techniques
99_Templates/         Gabarits réutilisables
```

---

## 🔄 Travailler à plusieurs (Git)

```bash
# Récupérer les dernières modifs avant de travailler
git pull

# Après avoir modifié des fichiers
git add <fichiers modifiés>
git commit -m "docs(seance3): description courte"
git push
```

**Règles d'équipe :**
1. Toujours `git pull` **avant** de commencer une séance.
2. Commits courts et clairs (préfixe : `docs`, `feat`, `fix`, `chore`).
3. Toute manipulation technique produit au moins **une preuve** dans `10_Documentation/07_Preuves/`.
4. Toute information non documentée est considérée comme **non réalisée**.

---

## 📊 État du projet (au 18/06/2026)

| Phase | État |
|---|---|
| S1 — Analyse, choix de l'outil, architecture | ✅ Terminé |
| S2 — Install VirtualBox + serveur FOG opérationnel, PXE validé (CLIENT A) | ✅ Terminé |
| S3 — Centralisation doc, dossier de reprise, capture image Windows | 🔄 En cours |
| S4+ — Déploiement clients, tests, restauration, défense orale | ⏳ À faire |

Détail complet : [`30_Dossier_Reprise/Dossier_Reprise.md`](30_Dossier_Reprise/Dossier_Reprise.md)
