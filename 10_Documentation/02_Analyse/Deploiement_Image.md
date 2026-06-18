# Déploiement d'image, matière brute

Notes pour la section correspondante du rapport d'analyse.

## Ce qu'est une image système

Une image système est une copie complète, octet par octet ou partition par partition, d'un disque dur ou d'une partition, stockée dans un fichier unique ou un ensemble de fichiers. Elle contient le système d'exploitation, les pilotes, les logiciels installés, les configurations utilisateur et toutes les données présentes sur le disque source au moment de la capture.

Une image système se distingue d'une sauvegarde de fichiers classique par le fait qu'elle restaure un système entièrement opérationnel et démarrable, et non un simple ensemble de documents.

## Formats courants

- WIM (Windows Imaging Format), utilisé nativement par Microsoft.
- VHD ou VHDX, formats Microsoft de disques virtuels également utilisables comme images de déploiement.
- Format propriétaire FOG, basé sur partclone et compressé via gzip ou zstd.
- Format dd brut, image bit à bit de tout le disque, lisible par presque tous les outils mais volumineux.

## Avantages du déploiement par image

- Gain de temps massif sur les déploiements en série.
- Standardisation absolue du parc.
- Reproductibilité : un poste défaillant peut être restauré à un état connu en quelques minutes.
- Réduction des erreurs humaines d'installation.
- Possibilité de tester l'image en amont, ce qui élimine les surprises lors du déploiement.

## Limites du déploiement par image

- Une image est figée au moment de sa capture. Toute mise à jour ultérieure nécessite soit une recapture, soit des scripts post-déploiement.
- Les images peuvent être lourdes (15 à 50 Go pour une image Windows 10 standard), ce qui sollicite le réseau et le stockage.
- Le matériel doit être suffisamment homogène. Une image capturée sur un modèle de PC peut nécessiter des ajustements de pilotes pour un autre modèle.
- Le déploiement écrase tout le contenu du disque cible, ce qui exige une vigilance sur les données existantes.

## Risques liés au déploiement d'image

- Image corrompue lors de la capture ou du transfert, conduisant à un poste inutilisable après déploiement.
- Mauvaise sélection de la cible, conduisant à l'écrasement de données critiques sur un mauvais poste.
- Pilotes manquants pour le matériel cible, conduisant à un système instable au premier démarrage.
- Identifiants système ou clés produit Windows non régénérés après déploiement, créant des conflits sur le réseau si Sysprep n'a pas été utilisé.
- Configurations sensibles (mots de passe administrateur, certificats, clés SSH) incluses involontairement dans l'image et propagées sur l'ensemble du parc.
- Image non testée déployée en production directe, propagation d'un défaut à grande échelle.

## Bonnes pratiques de réduction des risques

- Toujours préparer l'image source avec Sysprep côté Windows pour réinitialiser le SID et la clé produit.
- Tester chaque image sur au moins un poste pilote avant déploiement de masse.
- Versionner les images et conserver l'historique pour permettre un retour arrière.
- Documenter le contenu exact de chaque image (OS, version, logiciels, date de capture).
- Vérifier l'intégrité de l'image via hash SHA256 après transfert.
