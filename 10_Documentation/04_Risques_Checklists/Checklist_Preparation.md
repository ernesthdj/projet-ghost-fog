# Checklist de préparation

Liste des vérifications à effectuer avant toute opération technique sur le lab. Cette checklist est exécutée par le Responsable Qualité et Tests (Nicolas) en début de chaque séance technique.

## Matériel disponible

- [ ] Portable Ernest présent et fonctionnel
- [ ] Portable Renan présent et fonctionnel
- [ ] Portable Nicolas présent et fonctionnel
- [ ] Switch 8 ports présent et alimenté
- [ ] Câbles Ethernet en quantité suffisante et testés

## Réseau disponible

- [ ] Segment isolé via switch monté
- [ ] Aucun équipement parasite branché sur le switch
- [ ] Wi-Fi des portables actif pour Internet, distinct du segment lab
- [ ] DHCP du réseau host-only VirtualBox désactivé sur chaque portable

## Machines virtuelles

- [ ] VM serveur FOG installée sur le portable Ernest
- [ ] VM serveur FOG démarrée et accessible via son interface web
- [ ] VM source Windows 10 disponible sur le portable Ernest pour la capture
- [ ] VM cliente cible vide créée sur le portable Renan
- [ ] VM cliente cible vide créée sur le portable Nicolas
- [ ] Ordre de boot Network en premier configuré sur les VMs clientes

## Espace disque

- [ ] Minimum 80 Go libres sur le portable Ernest
- [ ] Minimum 40 Go libres sur les portables Renan et Nicolas
- [ ] Espace dans la VM FOG suffisant pour le dépôt d'images, 50 Go minimum

## Droits administrateur

- [ ] Compte administrateur disponible sur le portable Ernest
- [ ] Compte administrateur disponible sur le portable Renan
- [ ] Compte administrateur disponible sur le portable Nicolas
- [ ] Identifiants root et mot de passe FOG admin connus et documentés

## Documentation prête

- [ ] Vault Markdown synchronisé via OneDrive
- [ ] Rapport de séance ouvert pour la séance en cours
- [ ] Plan d'attaque à jour
- [ ] Journal Agile à jour
- [ ] Documentation officielle FOG ouverte ou téléchargée localement

## Sécurité

- [ ] Mot de passe admin FOG modifié, plus de valeur par défaut
- [ ] Aucune image contenant des données sensibles à risque de propagation
- [ ] Sauvegarde de la VM FOG effectuée avant toute manipulation risquée

## Synthèse

Total des points de contrôle : 22

Le minimum exigé par la consigne est de 10 points. La checklist dépasse cette exigence pour couvrir l'ensemble des aspects critiques avant toute manipulation technique.
