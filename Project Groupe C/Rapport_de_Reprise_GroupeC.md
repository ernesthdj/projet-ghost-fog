# Rapport de reprise du projet « Serveur Ghost »

Séance 4, Phase 2 (Transfert du projet). Projet repris du Groupe C.

Pour rappel du cadre : notre équipe a reçu le projet d'un autre groupe et doit le reprendre en s'appuyant
seulement sur les documents transmis, sans pouvoir, dans un premier temps, poser de questions à l'équipe
d'origine.

| Champ | Valeur |
|---|---|
| Équipe repreneuse | Ernest Hadjiu, Renan Demeereleere, Nicolas Terroir |
| Projet repris | Déploiement serveur Ghost |
| Équipe d'origine | Théo C, Théo W, Mamadou |
| Documents reçus | Projet groupe deploiement serveur Ghost.pdf, dossier_de_reprise (1).pdf |
| Date de reprise | 25/06/2026 |

## Phase 3 : Analyse du dossier reçu

Nous avons commencé par lire les deux documents en entier avant de toucher à quoi que ce soit. Voici, élément
par élément, ce que nous avons réellement compris.

| Élément | Compris ? | Commentaires |
|---|---|---|
| Objectif du projet | Oui | L'objectif revient dans les deux documents et il est clair : créer une image complète d'un disque (une copie fidèle) à l'aide d'un serveur Ghost, pour s'en servir ensuite comme sauvegarde et comme modèle à recopier sur d'autres postes. Le document rappelle aussi l'objectif pédagogique, à savoir apprendre à travailler en équipe sur les phases de planification, de préparation et de validation. |
| Architecture | Partiel | On trouve seulement un schéma de principe (un serveur relié par le réseau à deux postes, l'un qui envoie son image, l'autre qui la récupère). L'architecture concrète, elle, n'y est pas : aucun plan d'adressage IP, aucune caractéristique des machines virtuelles, pas d'hyperviseur nommé, et pas de mode réseau précisé. La checklist de la séance 1 parle bien d'un réseau isolé et d'un service DHCP de déploiement, mais sans donner la moindre valeur réelle. |
| État d'avancement | Oui | L'état d'avancement est décrit honnêtement. La compréhension du sujet est acquise, l'installation du logiciel a été lancée mais a posé beaucoup de problèmes, et le dossier de reprise comme le rapport journalier sont rédigés. En revanche, la phase de test n'a pas été faite. Le groupe résume lui-même son travail comme « en cours de peaufinage ». |
| Procédures | Non | C'est le point qui nous bloque. Les procédures d'installation et de configuration renvoient simplement à « voir procédure discord », un document qui ne nous a pas été transmis et auquel nous n'avons pas accès. Quant à la procédure de test, elle n'existe pas encore. Concrètement, rien de reproductible ne nous est fourni de ce côté. |
| Risques identifiés | Partiel | Le rapport d'analyse de la séance 1 propose un vrai tableau de risques (mauvaise image, perte de données, erreur de procédure, mauvaise configuration réseau, manque d'espace disque, mise à jour oubliée), avec pour chacun un impact et une parade. Mais dans le dossier de reprise, la section « Analyse de risque » est restée vide : il n'y a que les titres. Les deux documents ne disent donc pas la même chose. |
| Travail restant | Oui | Il est annoncé sans détour : terminer l'installation, réaliser les tests, puis mettre la documentation à jour. Cela colle bien avec l'état d'avancement décrit plus haut. |

### Notre principale réserve

En lisant les deux documents côte à côte, une chose nous gêne : on ne sait pas vraiment quel logiciel est
utilisé. Le rapport d'analyse parle longuement de Symantec Ghost, un logiciel commercial. Le dossier de reprise,
lui, mentionne le PXE (le démarrage d'une machine directement depuis le réseau, sans clé ni disque) et une
installation sur un système d'exploitation serveur. Ces deux pistes ne décrivent pas le même outil, et aucune
version n'est donnée. C'est, pour nous, le vrai angle mort du dossier.

## Phase 4 : Recherche des informations manquantes

Une fois les manques repérés, nous avons listé les informations qu'il nous faudrait absolument pour avancer,
et mesuré ce que leur absence coûte au projet.

| Information recherchée | Trouvée ? | Impact sur le projet |
|---|---|---|
| Les procédures d'installation et de configuration (celles renvoyées vers Discord) | Non | C'est le blocage le plus lourd. Sans elles, on ne peut ni reproduire ni reprendre l'installation. Il faut tout reconstituer, ce qui fait perdre beaucoup de temps. |
| Le nom et la version exacts du logiciel serveur | Non | Impact élevé. C'est lui qui conditionne toute la suite (façon d'installer, méthode de capture et de déploiement, compatibilité avec l'OS). L'incertitude vient justement du désaccord entre les deux documents. |
| Le plan d'adressage IP et la configuration réseau (adresse du serveur, plage DHCP, mode réseau, passerelle) | Non | Impact élevé. Le démarrage réseau et le déploiement ne fonctionnent pas sans un réseau maîtrisé. Le « problème de wifi » signalé laisse d'ailleurs penser que le réseau n'était pas stable. |
| Les caractéristiques de l'environnement (hyperviseur, mémoire, disque, OS hôte) | Non | Impact moyen. Il nous en faudrait pour recréer un environnement identique et reproduire le souci de compatibilité. |
| Le rôle exact de Windows Server 2019 (héberge-t-il le serveur, ou est-ce le poste à imager ?) | À déduire | Impact moyen. Le dossier dit seulement que « ça a marché sur Windows Server 2019 », sans préciser son rôle. Nous partons de l'idée qu'il héberge le serveur, mais c'est à confirmer. |
| Le détail du « problème de wifi » (sur quelle machine, résolu ou non ?) | Non | Impact moyen. Un déploiement par le réseau demande une liaison filaire fiable ; si ce problème n'est pas réglé, il peut tout rebloquer au moment des tests. |
| Les comptes et identifiants (administrateur du serveur, de l'OS) | Non | Impact moyen. Ils seraient nécessaires pour reprendre l'environnement existant tel quel. |

## Phase 5 : Reprise du projet

### 1. La prochaine étape que nous retenons

Reconstituer d'abord une procédure d'installation et de configuration claire et reproductible, puis lancer la
première vraie phase de test : capturer l'image d'un poste « cobaye » et la redéployer sur un second poste.

### 2. Pourquoi ce choix

1. Le travail restant indiqué par le groupe est limpide : terminer l'installation et faire les tests. La suite logique est donc clairement la phase de test.
2. Sauf qu'on ne peut pas s'y attaquer directement. Les procédures sont sur Discord, qu'on n'a pas, et aucun test n'a été documenté. On ne peut pas tester ce qu'on ne sait pas installer.
3. La première chose vraiment utile est donc de lever les blocages côté documentation : fixer l'outil, l'OS du serveur et le réseau, puis réécrire une procédure exploitable. C'est exactement l'esprit de l'exercice : un projet doit pouvoir être repris sans son auteur.

### 3. Ce que nous avons pu faire avec les seuls documents reçus

- Analyser les deux documents et en extraire l'objectif, l'architecture et le travail restant (voir Phase 3).
- Dresser la liste des informations manquantes (Phase 4), pour préparer nos questions au retour des auteurs (Phase 6).
- Trancher provisoirement la question de l'outil : nous partons sur un serveur d'images en réseau avec PXE, qui colle mieux au dossier de reprise et au schéma que le Symantec Ghost décrit dans l'analyse. À confirmer en Phase 6.
- Reconstituer un premier squelette de procédure d'installation (ci-dessous), à partir de la checklist de la séance 1, qui est, elle, utilisable.

### 4. Procédure d'installation reconstituée (à valider)

Reconstruite à partir de la checklist de préparation du rapport de séance 1 et de l'incident de compatibilité
décrit. Elle remplace, en attendant, le renvoi Discord manquant.

1. Préparer l'hôte de virtualisation, avec un réseau isolé pour ne pas perturber celui de l'école.
2. Créer la machine virtuelle du serveur sous Windows Server 2019, le seul système qui a fonctionné pour le groupe (Windows 10 et Windows Server 2022 ont échoué).
3. Fixer l'adressage : une adresse fixe pour le serveur et une plage réservée au déploiement (valeurs à définir, elles manquent au dossier).
4. Installer et configurer le logiciel serveur d'images. C'est l'étape qu'il faut entièrement re-documenter.
5. Préparer un poste cobaye et faire un instantané de la machine vierge avant toute manipulation, comme le recommandait la séance 1.
6. Lancer le test de bout en bout : capturer l'image du cobaye, la stocker sur le serveur, puis la redéployer sur un second poste via le réseau.
7. Vérifier le résultat, puis mettre la documentation à jour.

### 5. Trace de nos actions

Ce rapport est lui-même la trace de notre reprise : l'analyse menée, les manques identifiés, la décision prise
et la procédure reconstituée. Les actions techniques à venir seront notées à la suite.

## Phase 6 : Retour des anciens responsables

Quand les auteurs redeviennent disponibles, ils interviennent comme personnes ressources : ils répondent à nos
questions mais ne font pas le travail à notre place. Voici les questions que nous leur poserions, des plus
urgentes aux moins urgentes.

1. Pouvez-vous nous transmettre la procédure Discord d'installation et de configuration ? (le plus urgent)
2. Quel logiciel avez-vous installé exactement, et dans quelle version : Symantec Ghost, FOG, ou autre chose ? (le plus urgent)
3. Quel est le rôle de Windows Server 2019 : il héberge le serveur, ou bien c'est le poste à imager ?
4. Quel hyperviseur avez-vous utilisé, et avec quelles ressources (mémoire, disque) ?
5. Quel plan d'adressage IP (adresse du serveur, passerelle, plage DHCP) et quel mode réseau ?
6. Le problème de wifi a-t-il été réglé ? Le déploiement passe-t-il bien par une liaison filaire ?
7. Y a-t-il des identifiants (administrateur du serveur ou de l'OS) à nous communiquer ?

## Rapport de reprise

### 1. Ce que nous avons compris facilement

L'objectif du projet (un serveur d'images disque pour sauvegarder et cloner des postes) et le travail qui reste
à faire (finir l'installation, tester, mettre à jour la doc). Le schéma de principe aide beaucoup : on saisit
tout de suite l'idée du serveur qui reçoit puis renvoie l'image aux postes.

### 2. Ce qui était bien documenté

Toute la partie théorique de la séance 1 est solide : la définition d'une image système, ses avantages, ses
limites, ses risques, et les questions de réflexion (maintenance, cyber-résilience, lien avec ITIL, le
référentiel de bonnes pratiques informatiques). La checklist de préparation est complète et directement
réutilisable. Le glossaire du dossier de reprise est clair et pédagogique. Et l'état d'avancement est honnête,
les difficultés ne sont pas cachées.

### 3. Ce qui nous a manqué

Les procédures réelles (renvoyées vers Discord, donc absentes pour nous), le nom et la version exacts du
logiciel, le plan d'adressage réseau et les caractéristiques de l'environnement. Sans oublier la section risques
du dossier de reprise, restée vide. En résumé, tout ce qui est concret et reproductible nous a fait défaut.

### 4. Difficultés rencontrées

Notre principale difficulté a été de comprendre quel outil est vraiment utilisé, à cause de la contradiction
entre le Symantec Ghost de la théorie et le PXE évoqué en pratique. Sans procédure, impossible non plus d'avancer
techniquement sans tout reconstruire d'abord. Enfin, l'écart entre les risques bien remplis en séance 1 et la
section vide du dossier de reprise nous a obligés à choisir laquelle des deux versions croire.

### 5. Temps perdu à rechercher des informations

Nous avons passé l'essentiel de notre temps à chercher des informations absentes (les procédures, l'outil, le
réseau) plutôt qu'à faire avancer le projet. Le renvoi « voir Discord » en est la cause principale : une
information citée mais non jointe revient exactement au même qu'une information manquante.

### 6. Recommandations pour améliorer le dossier

- Mettre les procédures directement dans le dossier, sans jamais renvoyer vers un canal extérieur comme Discord. Le dossier doit se suffire à lui-même.
- Nommer précisément l'outil et sa version, avec le lien de téléchargement et de quoi vérifier le fichier.
- Donner le plan d'adressage IP et la configuration réseau avec de vraies valeurs, pas seulement « réseau isolé ».
- Décrire l'environnement utilisé (hyperviseur, mémoire, disque, système).
- Remplir la section risques du dossier de reprise, et la rendre cohérente avec le tableau de la séance 1.
- Prendre des captures d'écran à chaque étape réussie, en particulier pour le passage de Windows Server 2022 à 2019.

### 7. Travail réalisé durant la séance

Nous avons lu et analysé les deux documents reçus, complété les tableaux d'analyse (Phase 3) et des informations
manquantes (Phase 4), choisi et justifié la prochaine étape (Phase 5), reconstitué un premier squelette de
procédure d'installation à partir de la checklist de la séance 1, préparé nos questions pour le retour des
auteurs (Phase 6), et rédigé le présent rapport.

### Comment nous nous situons sur les critères d'évaluation

| Critère | Comment nous y répondons |
|---|---|
| Comprendre un projet existant | Chaque élément est analysé et qualifié en Phase 3 (Oui, Partiel ou Non). |
| Utilisation de la documentation | Nous nous appuyons uniquement sur les deux documents reçus et sur la checklist de la séance 1. |
| Esprit d'analyse | Nous avons relevé les incohérences : l'outil ambigu, la section risques vide, le renvoi vers Discord. |
| Qualité du rapport de reprise | Un document structuré, les sept points complétés, des recommandations concrètes. |
| Capacité à poursuivre le projet | La prochaine étape est identifiée et une procédure de départ est reconstituée (Phase 5). |
| Communication d'équipe | Des questions classées par priorité, prêtes pour le retour des auteurs (Phase 6). |
