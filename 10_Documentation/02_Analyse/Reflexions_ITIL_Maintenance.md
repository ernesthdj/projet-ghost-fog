# Questions de réflexion, matière brute

## Maintenance

FOG facilite principalement la maintenance corrective et la maintenance évolutive.

Sur le volet corrective, lorsqu'un poste tombe en panne logicielle (corruption du système, infection, profil utilisateur cassé), la procédure de remise en état devient extrêmement courte. Plutôt que de diagnostiquer en profondeur, le technicien peut redéployer l'image de référence et restituer un poste fonctionnel en moins d'une heure. Cela transforme une opération de plusieurs heures en une opération courte et reproductible.

Sur le volet évolutive, FOG permet de propager une nouvelle version d'image (mise à jour de l'OS, nouveau logiciel métier, patch de sécurité critique) sur l'ensemble du parc de manière coordonnée. La maintenance préventive de masse devient possible.

À l'inverse, FOG n'aide pas la maintenance préventive individuelle, ni le diagnostic de panne matérielle, qui restent du ressort des outils classiques d'administration et de monitoring.

## Cyber-résilience

Après un incident de sécurité (ransomware, compromission par malware, intrusion sur un poste), la priorité opérationnelle est de revenir à un état fiable rapidement. FOG répond directement à ce besoin.

Plutôt que de tenter une désinfection dont on ne sera jamais certain qu'elle a tout éradiqué, le redéploiement d'une image saine garantit un retour à un état connu, vérifié et propre. Le poste compromis est entièrement réécrit, ce qui élimine les rémanences potentielles dans des partitions cachées ou des zones non scannées par un antivirus.

FOG s'inscrit ainsi dans une stratégie de cyber-résilience moderne, qui ne cherche plus seulement à empêcher l'incident mais aussi à minimiser le temps de retour à la normale (RTO, Recovery Time Objective).

Important à mentionner dans le rapport : un redéploiement par FOG ne protège que la partie système. Les données utilisateurs spécifiques (documents, profils, configurations personnelles) doivent être traitées séparément par une stratégie de sauvegarde dédiée.

## Sauvegarde

Non, FOG ne remplace pas une sauvegarde.

FOG capture l'état du système à un instant donné, généralement celui d'un poste fraîchement préparé sans données utilisateur réelles. Une fois capturée, l'image vit indépendamment des évolutions du poste source. Si les utilisateurs créent ou modifient des fichiers sur leurs postes, ces données ne sont pas sauvegardées par FOG.

La logique de FOG est celle d'une image de référence, pas d'une copie périodique des données vivantes. Une vraie stratégie de sauvegarde implique des copies régulières, automatiques, datées, avec rotation et conservation, idéalement selon le principe 3-2-1 (trois copies sur deux supports différents dont une hors site).

FOG et la sauvegarde sont donc complémentaires, pas substituables. L'un protège le socle système, l'autre protège les données. Confondre les deux dans un plan de continuité serait une erreur grave.

## ITIL, continuité de service et gestion des incidents

ITIL (Information Technology Infrastructure Library, référentiel de bonnes pratiques de gestion des services informatiques) définit plusieurs processus dans lesquels FOG s'intègre naturellement.

Sur la gestion des incidents, FOG raccourcit drastiquement le temps moyen de résolution (MTTR, Mean Time To Repair). Un incident classé majeur sur un poste utilisateur peut passer de plusieurs heures à moins d'une heure, ce qui améliore directement les indicateurs de qualité de service. La procédure de remise en état devient prévisible et documentée, ce qui correspond exactement à l'esprit ITIL.

Sur la continuité de service, FOG contribue à la capacité de l'organisation à absorber un incident sans interruption durable de l'activité. Pour les contextes où plusieurs postes doivent rester opérationnels en permanence (salles de cours, postes de production, postes d'accueil), la possibilité de redéployer rapidement un poste de remplacement préconfiguré est un élément clé du plan de continuité.

Limite à mentionner : FOG seul ne constitue pas un plan de continuité complet. Il doit s'intégrer dans une démarche plus large incluant la gestion des sauvegardes de données, le suivi des configurations (CMDB, Configuration Management Database, base de données des éléments d'infrastructure), et la documentation des procédures de reprise.
