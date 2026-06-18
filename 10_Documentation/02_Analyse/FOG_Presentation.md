# FOG Project, matière brute

Notes de travail pour alimenter le rapport d'analyse.

## Définition

FOG est un logiciel libre de déploiement d'images système distribué sous licence GPL. Son serveur s'installe sur une distribution Linux et expose une interface d'administration web. Il s'appuie sur les services TFTP, HTTP, MySQL et un démon PXE pour permettre à des postes clients de démarrer sur le réseau, de capturer ou de recevoir une image disque, et d'exécuter des scripts d'installation post-déploiement.

Le nom du projet vient de l'acronyme Free Open-source Ghost, en référence à Symantec Ghost, l'outil historique de clonage de disque dont FOG reprend la logique fonctionnelle en version communautaire.

## À quoi sert FOG

À automatiser la mise en service de postes informatiques en évitant l'installation manuelle, poste par poste, du système d'exploitation, des logiciels métier et des configurations. Un parc de plusieurs dizaines ou centaines de machines peut ainsi être déployé ou restauré en quelques heures au lieu de plusieurs jours.

FOG sert également à standardiser les postes : tous reçoivent strictement la même image, ce qui garantit un environnement homogène, plus simple à maintenir et à dépanner.

## Problèmes que FOG résout

Sans solution de déploiement d'image, l'installation d'un système d'exploitation, des pilotes, des mises à jour et des logiciels métier représente plusieurs heures par poste. À l'échelle d'une salle informatique de 20 postes, cela représente une charge de plusieurs jours-homme.

FOG résout également la difficulté de maintenir des postes identiques dans le temps. Au fil des dépannages individuels, les configurations divergent et créent ce qu'on appelle la dérive de configuration. Avec FOG, un poste défaillant peut être réinitialisé en quelques minutes à partir d'une image de référence connue, propre et à jour.

## Contextes d'utilisation typiques

- Établissements scolaires et universitaires gérant des salles informatiques pédagogiques.
- Entreprises avec un parc de postes utilisateurs standardisés.
- Services informatiques publics avec des contraintes budgétaires fortes excluant les solutions propriétaires.
- Laboratoires de test où des machines doivent être réinitialisées fréquemment à un état connu.
- Centres de formation continue avec rotation rapide des stagiaires et besoin de remise à zéro régulière des postes.

## Architecture technique en bref

Le serveur FOG centralise les images disque dans un dépôt local. Les postes clients démarrent via PXE, ce qui leur permet de charger un mini-système Linux depuis le serveur sans toucher à leur disque dur. Une fois ce mini-système chargé, l'administrateur déclenche depuis l'interface web soit une capture (le disque du poste vers une nouvelle image sur le serveur), soit un déploiement (une image existante vers le disque du poste).

Le serveur intègre également un service DHCP optionnel et un service TFTP pour fournir le bootloader PXE aux clients.

## Modes de déploiement

- Unicast : une image envoyée à un seul client à la fois, simple mais lent à grande échelle.
- Multicast : une image envoyée simultanément à plusieurs clients via un flux unique sur le réseau, performant et économe en bande passante.

Pour notre lab à 2 clients, le multicast n'apporte pas de gain mesurable mais reste pertinent à documenter comme capacité native de FOG.

## Compatibilité

FOG prend en charge les images de systèmes Windows (de Windows XP à Windows 11), les distributions Linux courantes et macOS dans certaines limites. Il gère les disques BIOS legacy et UEFI, les schémas de partition MBR et GPT.

## Notes utiles pour la défense orale

- FOG existe depuis 2007, projet mature.
- Communauté active sur le forum officiel et sur GitHub.
- Pas de licence à payer, pas de quota d'utilisateurs, pas de limite de postes.
- Documentation officielle complète disponible sur docs.fogproject.org.
