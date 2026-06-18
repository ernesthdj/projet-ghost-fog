# Checklist de préparation

Vérifications à effectuer **en début de chaque séance technique**, avant toute manipulation.
Exécutée par le Responsable Qualité et Tests (Nicolas), ou par le membre présent en cas d'absence.

## 0. Continuité (nouveau — S3)

- [ ] `git pull` effectué : la doc locale est à jour avec le dépôt
- [ ] Membres présents identifiés et rôles du jour répartis (gérer les absences éventuelles)

## 1. Matériel & postes

- [ ] Les postes nécessaires sont présents et fonctionnels
- [ ] Droits administrateur disponibles sur chaque poste utilisé
- [ ] Hyper-V désactivé (si un PC a été réinitialisé) : `bcdedit /set hypervisorlaunchtype off`

## 2. Réseau

- [ ] Toutes les VMs impliquées (FOG + master + clients) sur le **même réseau** VirtualBox
- [ ] Mode réseau confirmé (Bridged / Internal Network — **pas NAT isolé**) ; voir note réseau de la procédure
- [ ] FOG en **proxyDHCP** (ne distribue pas d'IP, pas de conflit avec le DHCP de la salle)
- [ ] Connectivité vérifiée (interface web FOG accessible depuis les postes)

## 3. Serveur FOG

- [ ] VM FOG démarrée, interface web accessible (`http://<IP-FOG>/fog/management`)
- [ ] Services FOG actifs (`systemctl status fog*`)
- [ ] Mot de passe admin FOG modifié (plus la valeur par défaut)
- [ ] Espace disque suffisant sur la VM FOG pour stocker l'image

## 4. Préparation du Master (séances capture)

- [ ] ISO Windows 10 Pro 22H2 disponible
- [ ] VM `Win10-Master` créée : disque 40 Go, réseau OK, **boot Réseau en 1er**, BIOS legacy
- [ ] **Snapshot `avant-sysprep`** pris avant toute exécution de Sysprep
- [ ] Adresse MAC du master relevée et notée

## 5. Déploiement (séances déploiement)

- [ ] VM(s) cliente(s) vide(s) créée(s), disque ≥ taille de l'image, boot Réseau en 1er
- [ ] Adresses MAC des clients relevées et enregistrées dans FOG
- [ ] Image `Win10-Pro-22H2` présente sur le serveur

## 6. Documentation & preuves

- [ ] `Rapport_Seance_XX.md` ouvert pour la séance en cours
- [ ] Plan d'attaque et journal Agile prêts à être complétés
- [ ] Dossier `07_Preuves/` prêt à recevoir les captures du jour

## 7. Sécurité & sauvegarde

- [ ] Aucune donnée sensible dans l'image à déployer
- [ ] **Export `.ova` de la VM FOG** effectué avant toute manipulation risquée
- [ ] Snapshots pris avant les opérations irréversibles

---

> Cette checklist couvre l'ensemble du cycle (installation → capture → déploiement). Cocher
> uniquement les sections pertinentes pour la séance en cours.
