# Reefmonitor

Reefmonitor est un moniteur ESPHome de paramètres d'eau pour aquarium récifal.

## Objectif

Suivre les paramètres utiles au contrôle courant d'un aquarium récifal et les exposer proprement dans Home Assistant.

## Périmètre actuel

- mesure ORP ;
- mesure pH ;
- mesure conductivité / salinité ;
- mesure température Dallas ;
- capteur niveau haut décante ;
- capteur niveau bas décante ;
- exposition des entités dans Home Assistant via l'API ESPHome.

## Matériel utilisé

- ESP32 ;
- sonde Dallas DS18B20 ;
- circuits EZO Atlas Scientific pour `pH`, `ORP` et `conductivité` ;
- capteurs optiques pour le niveau haut et le niveau bas de décante.

## Intégration Home Assistant

Le dépôt est structuré pour une exposition automatique des entités dans Home Assistant via l'API ESPHome.

Entités principales attendues :
- `sensor.ph`
- `sensor.orp`
- `sensor.conductivit_`
- `sensor.salinit_`
- `sensor.temp_rature`
- `binary_sensor.capteur___haut`
- `binary_sensor.capteur___bas`
- `text_sensor.niveau_d_eau`

## Structure du dépôt

- `install.yaml` : point d'entrée ESPHome ;
- `reefmonitor_config.yaml` : configuration du nœud ;
- `common/` : fragments YAML réutilisables ;
- `docs/` : documentation projet ;
- `secrets.yaml` : secrets locaux non versionnés.

## Documentation

Le guide matériel est disponible dans [docs/hardware.md](docs/hardware.md).

Le détail de l'audit initial est conservé dans [docs/audit_perimetre_initial_2026-07-10.md](docs/audit_perimetre_initial_2026-07-10.md).
