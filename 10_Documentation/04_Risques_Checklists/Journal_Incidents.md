# Journal des incidents

> Registre vivant de tous les incidents techniques rencontrés. À compléter **dès qu'un problème
> survient**, pendant la séance. Format : symptôme → cause → solution → statut.

| # | Date | Incident (symptôme) | Cause | Solution appliquée | Statut |
|---|---|---|---|---|---|
| 1 | 11/06/2026 (S2) | VM Ubuntu 64 bits refuse de démarrer dans VirtualBox | Hyper-V actif sur Windows 11 monopolise la virtualisation (VT-x) | `bcdedit /set hypervisorlaunchtype off` en admin + redémarrage (sur les 3 PC) | ✅ Résolu |
| 2 | 11/06/2026 (S2) | Le script FOG refuse le nom d'interface réseau | Ubuntu nomme l'interface `enp0s3` (systemd) et non `eth0` | Identifier le nom réel avec `ip a` avant de lancer le script | ✅ Résolu |
| 3 | 11/06/2026 (S2) | Travail bloqué par l'absence de membres + doc non centralisée | Documentation locale sur un seul poste, pas de dépôt partagé | Mise en place du dépôt Git/GitHub `projet-ghost-fog` (S3) | ✅ Résolu |

---

## Incidents potentiels anticipés (non encore rencontrés)

À surveiller lors des phases capture/déploiement (voir
[`../05_Procedures/Procedure_Master_Deploiement.md`](../05_Procedures/Procedure_Master_Deploiement.md)) :

- VM ne boote pas en PXE → ordre de boot ou mode réseau.
- Master rallumé après Sysprep → image non généralisée (revenir au snapshot).
- Image déployée qui ne boote pas → cohérence BIOS legacy + type d'image.
- MAC erronée enregistrée dans FOG → tâche non déclenchée.

> Quand l'un de ces cas survient réellement, le déplacer dans le tableau principal ci-dessus avec sa date.
