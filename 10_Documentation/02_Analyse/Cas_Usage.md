# Cas d'utilisation, matière brute

## Avantages, plus de trois

1. Gain de temps considérable sur les déploiements de masse. Là où une installation manuelle prendrait deux à trois heures par poste, un déploiement par image se mesure en quinzaine de minutes.

2. Standardisation du parc informatique. Tous les postes reçoivent exactement la même configuration, ce qui simplifie le support, le dépannage et la formation des utilisateurs.

3. Restauration rapide après incident. Un poste compromis, infecté ou défaillant peut être ramené à un état sain en quelques minutes au lieu d'une réinstallation complète.

4. Réduction des erreurs humaines. L'automatisation supprime les oublis classiques de l'installation manuelle (logiciel oublié, paramètre mal configuré).

5. Coût opérationnel réduit pour les structures avec un parc important. Le temps technicien économisé représente plusieurs milliers d'euros par an dès quelques dizaines de postes.

## Inconvénients, plus de trois

1. Investissement initial en temps. La préparation de l'image de référence, sa validation et la configuration du serveur de déploiement représentent plusieurs jours de travail avant le premier déploiement utile.

2. Rigidité face à l'hétérogénéité matérielle. Si le parc comporte plusieurs modèles de PC différents, il faut soit maintenir plusieurs images, soit gérer dynamiquement les pilotes, ce qui complexifie l'exploitation.

3. Risque de propagation d'une erreur à grande échelle. Une mauvaise image déployée sur 50 postes affecte 50 postes simultanément. La validation préalable devient critique.

4. Dépendance au réseau et au serveur de déploiement. Un serveur FOG hors service ou un réseau saturé bloque toute opération de remise en service.

5. Image vieillissante. Sans recapture régulière, l'image accumule un retard sur les mises à jour de sécurité et nécessite un long cycle de patchs après chaque déploiement.

6. Effacement total de la cible. Le déploiement écrase tout, donc inadapté pour préserver des données locales utilisateur sans sauvegarde préalable.
