# Matrice comparative des outils de déploiement d'image

## Critères d'évaluation

Les outils sont évalués selon les critères suivants, alignés sur les contraintes du projet :

1. Coût et modèle de licence (priorité absolue : freemium ou gratuit pour public étudiant).
2. Complexité d'installation et courbe d'apprentissage.
3. Capacités de déploiement (unicast, multicast, scripts post-installation).
4. Compatibilité OS (Windows 10 prioritaire).
5. Communauté et documentation disponible.
6. Ressources matérielles requises pour le serveur.

## Comparaison

| Critère | FOG Project | Clonezilla Server Edition | WDS + MDT | Symantec Ghost Solution Suite | Acronis Snap Deploy |
|---|---|---|---|---|---|
| Coût | Gratuit, open source GPL | Gratuit, open source GPL | Gratuit avec Windows Server | Payant, licence pro | Payant, essai limité |
| OS serveur | Linux (Ubuntu, Debian) | Linux | Windows Server | Windows | Windows |
| Interface | Web responsive | Ligne de commande | Console MMC + ADK | Console propriétaire | Console propriétaire |
| Déploiement unicast | Oui | Oui | Oui | Oui | Oui |
| Déploiement multicast | Oui | Oui | Oui | Oui | Oui |
| Scripts post-installation | Oui (snapins, bash) | Limité | Oui (task sequences XML) | Oui | Oui |
| Compatibilité Windows 10 | Excellente | Bonne | Native | Excellente | Excellente |
| Documentation officielle | Très complète | Complète mais dispersée | Complète mais lourde | Réservée clients | Réservée clients |
| Communauté | Active, forums, GitHub | Active, forums | Massive (Microsoft) | Limitée | Limitée |
| Ressources serveur minimales | 2 vCPU, 4 Go RAM | 1 vCPU, 2 Go RAM | 2 vCPU, 8 Go RAM | Variable | Variable |
| Pertinence pour étudiants | Très forte | Forte | Forte si Windows Server dispo | Nulle (coût) | Faible (essai limité) |

## Conclusion et choix retenu

FOG Project est retenu comme outil de déploiement pour ce projet.

Justification synthétique :

1. Gratuité totale et licence libre, conformes à la contrainte étudiante.
2. Interface web moderne, accessible à un public en formation.
3. Documentation officielle abondante et tutoriels communautaires nombreux, ce qui sécurise l'apprentissage en autonomie.
4. Adoption massive en milieu éducatif francophone, ce qui rend le choix immédiatement défendable face à un évaluateur.
5. Ressources serveur modestes, compatibles avec une VM sur un portable étudiant.
6. Capacité multicast permettant une démonstration de déploiement parallèle si la démo facultative est tentée.

Clonezilla SE était une alternative crédible mais son ergonomie en ligne de commande pure aurait alourdi la production documentaire (captures moins parlantes, vocabulaire plus aride).

WDS plus MDT était techniquement excellent mais imposait un serveur Windows Server, donc une licence éducation à activer et une complexité de mise en oeuvre disproportionnée au temps disponible.

Les solutions propriétaires Symantec et Acronis sont éliminées d'office pour incompatibilité avec la contrainte budgétaire étudiante.
