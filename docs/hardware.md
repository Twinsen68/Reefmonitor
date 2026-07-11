# Guide Matériel Reefmonitor

Ce document décrit l'architecture matérielle retenue pour Reefmonitor et sert de synthèse de référence pour le câblage, les bus et les choix de conception.

## Vue d'ensemble

Reefmonitor repose sur un ESP32-S3 comme contrôleur central, avec :

- un bus I2C partagé pour les circuits Atlas Scientific EZO ;
- un bus 1-Wire dédié à la sonde Dallas de température ;
- deux entrées GPIO pour les capteurs optiques de niveau de décante ;
- l'exposition des mesures dans Home Assistant via ESPHome.

Le projet se concentre sur la mesure et la supervision. Il ne cherche pas, à ce stade, à piloter une électrovanne, une osmolation ou un changement d'eau.

## Contrôleur principal

### ESP32-S3

L'ESP32-S3 joue le rôle de maître de bus et de passerelle vers Home Assistant.

Rôles principaux :

- piloter le bus I2C des modules Atlas Scientific ;
- lire la sonde Dallas DS18B20 ;
- surveiller les capteurs de niveau haut et bas ;
- exposer les entités à Home Assistant ;
- fournir la maintenance à distance via ESPHome.

Points de conception retenus :

- un contrôleur unique pour centraliser les mesures ;
- une logique simple côté bus, pour réduire les risques de dérive de configuration ;
- une exposition Home Assistant directe, sans couche applicative supplémentaire.

## Cartes Atlas Scientific

Reefmonitor s'appuie sur deux générations de cartes Atlas Scientific pour les circuits EZO.

| Génération | Rôle dans le projet | Intérêt principal | Différence de philosophie |
| --- | --- | --- | --- |
| Isolated Carrier Gen2 | Référence historique du projet | Isolation galvanique et câblage robuste dans un environnement humide | Carte plus orientée "atelier" et intégration par circuit |
| i4 InterLink | Cible plus récente et plus compacte | Intégration plus propre et câblage simplifié autour de plusieurs sondes EZO | Carte plus orientée système et interconnexion |

### Ancienne carte Isolated Carrier Gen2

Cette génération est utile comme base de compréhension et de dépannage.

Atouts pratiques :

- isolation galvanique entre la partie contrôleur et la partie capteurs ;
- meilleure tolérance aux environnements perturbés ;
- logique de câblage très explicite ;
- bon choix pour valider un montage initial ou isoler un problème de terrain.

Limites dans un projet comme Reefmonitor :

- encombrement plus important ;
- câblage plus segmenté ;
- moins adaptée à une intégration compacte ;
- maintenance plus lente quand plusieurs circuits EZO sont présents.

### Nouvelle carte i4 InterLink

Cette génération correspond à une approche plus intégrée du système.

Atouts attendus pour Reefmonitor :

- montage plus compact ;
- interconnexion plus simple entre plusieurs modules ;
- câblage plus lisible sur une armoire ou une baie technique ;
- meilleure cohérence avec un projet centré sur plusieurs sondes EZO.

Différences pratiques avec la Gen2 :

- moins orientée "carte par carte" ;
- plus adaptée à une topologie de mesures regroupées ;
- meilleure adéquation avec une architecture propre pour Home Assistant.

### Choix de génération

Le projet peut conserver la Gen2 comme base de comparaison ou de secours, mais la direction matérielle doit rester cohérente avec l'objectif de réduire l'encombrement et la complexité de câblage.

Pour la documentation et le déploiement final, il reste à confirmer quelle génération est effectivement retenue comme solution cible.

## Sondes Atlas utilisées

### pH

La sonde pH sert à surveiller la stabilité chimique du bac.

Dans Reefmonitor, elle est exposée comme entité Home Assistant de mesure et dispose d'actions de lecture et de calibration.

### ORP

La sonde ORP mesure le potentiel d'oxydoréduction.

Elle complète le pH pour suivre l'état chimique global de l'eau.

### Conductivité

La sonde de conductivité fournit la mesure de base pour la salinité et les dérivés affichés par Reefmonitor.

Le projet en déduit aussi des valeurs secondaires comme la salinité et le TDS.

## Capteurs de niveau

Reefmonitor utilise deux capteurs optiques :

- niveau bas décante ;
- niveau haut décante.

Ils servent à qualifier l'état de la décante et à produire une lecture synthétique du niveau d'eau.

Points matériels importants :

- la polarité réelle doit être validée sur le montage ;
- l'inversion logique doit correspondre à la mécanique des flotteurs ou des optiques ;
- le comportement à l'état intermédiaire doit être confirmé en situation réelle.

## Architecture I2C

L'architecture I2C du projet est simple et centralisée.

### Bus principal

- SDA sur GPIO21 ;
- SCL sur GPIO22 ;
- scan I2C activé ;
- bus unique pour les circuits Atlas EZO.

### Adressage

Les adresses actuellement utilisées dans la configuration sont :

- pH : `99`
- ORP : `98`
- conductivité : `100`

Ces valeurs doivent rester alignées avec le câblage physique et avec la stratégie de calibration ou de reconfiguration des circuits.

### Principe de fonctionnement

Le maître I2C interroge les modules EZO, puis ESPHome publie les valeurs vers Home Assistant.

Ce choix limite la complexité logicielle côté ESP32-S3 et garde la couche de mesure facile à diagnostiquer.

## Bus et entrées complémentaires

### Dallas 1-Wire

La température Dallas DS18B20 est lue sur un bus 1-Wire séparé.

Ce choix évite de mélanger la lecture de température avec les mesures chimiques EZO.

### Entrées de niveau

Les deux capteurs de niveau utilisent des GPIO dédiés avec résistance de tirage interne et logique inversée.

Cette approche est adaptée à un capteur optique simple et facilite la lecture immédiate des états.

## Choix de conception du projet

Les choix matériels suivent une logique simple :

- séparer les fonctions de mesure par famille de capteurs ;
- garder un bus I2C unique pour les EZO ;
- réserver la température à un bus 1-Wire ;
- maintenir des entrées numériques séparées pour les niveaux ;
- exposer les états directement à Home Assistant ;
- garder un périmètre de surveillance clair avant d'ajouter d'autres fonctions.

Le projet est donc volontairement orienté "monitoring" plutôt que "pilotage".

## Points restant à confirmer

- le modèle exact de l'ESP32-S3 retenu ;
- la génération Atlas finalement retenue comme solution cible ;
- les adresses I2C finales si une reconfiguration est décidée ;
- l'alimentation définitive des cartes Atlas et du contrôleur ;
- la nécessité d'une isolation supplémentaire selon le câblage final ;
- la polarité effective des capteurs de niveau une fois montés ;
- la longueur maximale acceptable des liaisons I2C dans l'armoire technique ;
- la stratégie de fixation mécanique et de protection contre l'humidité.

## Références de travail

- [README du projet](../README.md)
- [Audit de périmètre initial](audit_perimetre_initial_2026-07-10.md)
