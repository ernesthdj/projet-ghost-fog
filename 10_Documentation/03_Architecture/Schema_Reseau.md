# Schéma réseau

## Représentation textuelle

```
                  +--------------------------+
                  |   Portable Ernest        |
                  |   Hôte VirtualBox        |
                  |                          |
                  |   VM Serveur FOG         |
                  |   Ubuntu Server 22.04    |
                  |   IP 192.168.50.10       |
                  +--------------------------+
                              |
                              | Ethernet, mode Bridged
                              |
                  +--------------------------+
                  |                          |
                  |     Switch 8 ports       |
                  |     Segment isolé        |
                  |                          |
                  +--------------------------+
                       |                |
                       |                |
        +----------------------+   +----------------------+
        | Portable Renan       |   | Portable Nicolas        |
        | Hôte VirtualBox      |   | Hôte VirtualBox      |
        |                      |   |                      |
        | VM Client Win 10     |   | VM Client Win 10     |
        | Boot PXE             |   | Boot PXE             |
        | IP dynamique FOG     |   | IP dynamique FOG     |
        +----------------------+   +----------------------+
```

## À produire avec draw.io

Le schéma textuel ci-dessus servira de référence pour produire un schéma graphique propre via draw.io. Format de sortie attendu : PNG haute résolution et PDF pour intégration au rapport final.

Éléments graphiques à inclure :

- Icônes serveur, clients, switch.
- Légende des protocoles utilisés.
- Encadré "segment isolé" englobant l'ensemble du lab.
- Code couleur pour distinguer flux PXE, flux HTTP d'administration, flux de transfert d'image.

Fichier source draw.io à conserver dans `07_Preuves` une fois produit.
