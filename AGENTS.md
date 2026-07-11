# Objectif du projet

Le projet Reefmonitor est un nœud ESPHome dédié à la surveillance d'un aquarium récifal.

À ce stade, son périmètre est volontairement limité à :

- mesure du pH ;
- mesure de l'ORP ;
- mesure de la conductivité / salinité ;
- mesure de la température (Dallas DS18B20) ;
- surveillance du niveau haut et bas de la décante ;
- exposition des données dans Home Assistant via ESPHome.

Le projet est un projet de monitoring.

Les fonctions de pilotage (osmolation, pompes, électrovannes, changement d'eau, etc.) sont hors périmètre tant qu'elles ne sont pas explicitement décidées.

# Architecture

L'architecture actuelle repose sur un nœud ESPHome qui collecte les mesures, publie les entités et reste la couche firmware du projet.

Home Assistant prend en charge les tableaux de bord, les historiques et les notifications.

Si des fonctions actives sont ajoutées un jour, les sécurités critiques devront rester locales et ne pas dépendre uniquement de Home Assistant.

# Matériel

Les choix matériels connus à ce stade sont les suivants :

- un ESP32-S3 comme contrôleur principal ;
- un bus I2C pour les circuits Atlas Scientific ;
- un bus 1-Wire pour la sonde Dallas ;
- des capteurs de niveau TOR pour le niveau haut et bas de la décante ;
- des cartes Atlas Scientific de génération Gen2 et i4 InterLink.

Les datasheets et PDF fabricants servent de référence technique, mais ne doivent pas être recopiés dans la documentation du projet.

# Connaissances acquises

- Reefmonitor est le dépôt de référence.
- Les autres dépôts sont conservés comme archives ou sources d'inspiration.
- La nouvelle carte Atlas Scientific i4 InterLink est la plateforme privilégiée.
- Les anciennes cartes Gen2 restent compatibles.
- Les capteurs de niveau sont des flotteurs TOR.
- Les GPIO sont définis dans le code ESPHome et ne doivent pas être dupliqués dans la documentation.
- Les adresses I²C sont à confirmer sur le matériel si elles diffèrent des valeurs par défaut.

# Règles de développement

Toujours :

- privilégier la simplicité ;
- éviter les duplications ;
- conserver une architecture modulaire ;
- documenter les nouvelles fonctionnalités ;
- conserver la compatibilité ESPHome.

Ne jamais :

- modifier plusieurs modules sans justification ;
- casser une fonctionnalité existante pour une optimisation esthétique ;
- supprimer une fonction sans validation.

# Documentation

Toute nouvelle fonctionnalité importante doit être documentée.

Le `README.md` présente le projet.

Le dossier `docs/` contient la documentation technique.

Les PDF Atlas servent de référence, mais ne doivent pas être recopiés.

# Documentation du code

Toute modification importante doit conserver ou améliorer la documentation interne.

Les commentaires doivent expliquer le "pourquoi" plutôt que le "quoi".

Ajouter un en-tête aux nouveaux fichiers importants.

# Workflow Codex

Avant toute modification :

1. comprendre le module ;
2. identifier les dépendances ;
3. proposer les modifications les plus simples ;
4. limiter le nombre de fichiers modifiés ;
5. afficher le diff final ;
6. ne jamais faire de commit ;
7. ne jamais faire de push.

# Modèles Codex

GPT-5.4-mini :

- documentation ;
- corrections mineures ;
- petites modifications YAML.

GPT-5.4 :

- nouvelles fonctionnalités ;
- refactoring local ;
- évolution d'un module.

GPT-5.5 :

- audit d'architecture ;
- conception globale ;
- restructuration importante.

# Vision du projet

Le projet doit rester un système de supervision fiable, évolutif et simple à maintenir.

Les futures évolutions devront préserver cette philosophie.

# Validation

Avant une modification importante :

- vérifier les dépendances ;
- vérifier que la configuration ESPHome reste compilable ;
- proposer un plan de test lorsque la modification touche le matériel ou les mesures.
