# Audit de périmètre initial - Reefmonitor

Date de l'audit : 2026-07-10

## 1. Périmètre fonctionnel actuel

Le dépôt Reefmonitor couvre actuellement le périmètre court terme suivant :

- mesure ORP ;
- mesure pH ;
- mesure conductivité / salinité ;
- mesure température Dallas ;
- capteur niveau haut décante ;
- capteur niveau bas décante ;
- exposition des entités dans Home Assistant.

## 2. Fonctions déjà présentes

### Mesure ORP

Présente dans [common/ezo_orp.yaml](../common/ezo_orp.yaml) avec :

- capteur `ORP` ;
- lecture manuelle via bouton `ORP - Lire` ;
- commande EZO via bouton `ORP - Envoi Cmd` ;
- sélecteur de commande ;
- modification de l'adresse I2C ;
- actions API de calibration et de lecture continue.

### Mesure pH

Présente dans [common/ezo_ph.yaml](../common/ezo_ph.yaml) avec :

- capteur `pH` ;
- lecture manuelle via bouton `pH - Lire` ;
- commande EZO via bouton `pH - Envoi Cmd` ;
- sélecteur de commande ;
- actions API de calibration ;
- texte de retour de commande.

### Mesure conductivité / salinité

Présente dans [common/ezo_ec.yaml](../common/ezo_ec.yaml) avec :

- capteur `Conductivité` ;
- texte brut `EC - Valeur brute` ;
- capteur `Salinité` ;
- capteur `TDS` ;
- capteur `Specific Gravity` ;
- lecture manuelle via bouton ;
- commande EZO via bouton ;
- actions API de calibration et de configuration des sorties.

### Mesure température Dallas

Présente dans [common/temperature_dallas.yaml](../common/temperature_dallas.yaml) avec :

- sonde `Température` ;
- calcul `Gamme Température` ;
- état binaire `Température correct` ;
- seuils configurables `Temp. basse` et `Temp. haute`.

### Niveau décante

Présent dans [common/water_level.yaml](../common/water_level.yaml) avec :

- capteur optique `Capteur : Bas` ;
- capteur optique `Capteur : Haut` ;
- synthèse `Niveau d'eau`.

### Exposition Home Assistant

Présente via l'API ESPHome dans [install.yaml](../install.yaml) et la configuration de base dans [common/device_base.yaml](../common/device_base.yaml).

## 3. Points à vérifier

- l'alignement entre les entités générées et le nommage attendu dans Home Assistant ;
- la validation terrain de la lecture salinité / conductivité ;
- la cohérence des entités de configuration exposées en plus des capteurs métier ;
- la robustesse de l'import de package ESPHome depuis `reefmonitor_config.yaml`.

## 4. Anomalies détectées

### Logique des boutons EZO

Les boutons de commande EZO utilisent une logique de sélection qui retombe sur une lecture par défaut. Cela doit être revu pour éviter des commandes involontaires ou ambiguës.

Fichiers concernés :
- [common/ezo_ph.yaml](../common/ezo_ph.yaml)
- [common/ezo_ec.yaml](../common/ezo_ec.yaml)
- [common/ezo_orp.yaml](../common/ezo_orp.yaml)

### `device_class` à vérifier

À vérifier : certaines `device_class` utilisées pour les capteurs de conductivité, salinité, TDS et Specific Gravity pourraient ne pas être les plus adaptées à leur usage dans Home Assistant.

Fichier concerné :
- [common/ezo_ec.yaml](../common/ezo_ec.yaml)

### Noms d'entités Home Assistant

Les entités générées contiennent des caractères accentués ou des transcriptions qui produisent des `object_id` peu lisibles dans Home Assistant.

Fichiers concernés :
- [common/ezo_ph.yaml](../common/ezo_ph.yaml)
- [common/ezo_ec.yaml](../common/ezo_ec.yaml)
- [common/ezo_orp.yaml](../common/ezo_orp.yaml)
- [common/temperature_dallas.yaml](../common/temperature_dallas.yaml)
- [common/water_level.yaml](../common/water_level.yaml)

### Casse du fichier `reefmonitor_config.yaml`

Le lien d'import dans la configuration fait référence à `Reefmonitor_config.yaml` avec une casse différente du nom local `reefmonitor_config.yaml`.

Fichier concerné :
- [common/reefmonitor.yaml](../common/reefmonitor.yaml)

### Polarité des capteurs de niveau

La polarité réelle des capteurs haut / bas doit être validée sur le montage pour confirmer que la logique `High / Normal / Low` est correcte.

Fichier concerné :
- [common/water_level.yaml](../common/water_level.yaml)

## 5. Éléments hors périmètre

Les éléments suivants ne sont pas à traiter dans cet audit :

- électrovanne ;
- osmolation ;
- changement d'eau ;
- pompes doseuses ;
- écran local.

## 6. Prochaines étapes recommandées

1. Valider en pratique les états des capteurs de niveau haut et bas.
2. Nettoyer les boutons EZO pour éviter toute commande implicite non voulue.
3. Réévaluer les `device_class` des capteurs de conductivité / salinité et corriger celles qui seraient inadaptées.
4. Renommer ou normaliser les entités pour obtenir des entités Home Assistant plus lisibles.
5. Corriger la casse du fichier utilisé pour l'import dans le tableau de bord ESPHome si l'on souhaite fiabiliser ce chemin.
