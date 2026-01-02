# Aquarium Monitor - ESPHome Configuration

## **Introduction**

**Aquarium Monitor** est un projet basé sur ESPHome, conçu pour automatiser la gestion et la surveillance des paramètres critiques d'un aquarium récifal. En combinant un microcontrôleur ESP32 avec des capteurs et actionneurs, ce système permet de suivre et d’optimiser en temps réel les conditions de vie marine, tout en intervenant automatiquement pour corriger les écarts.

### **Fonctionnalités Principales**
1. **Surveillance de la Température** :
   - Mesure précise à l’aide de sondes Dallas DS18B20.
   - Détection des températures critiques avec alertes et diagnostics dans Home Assistant.
   - Plages ajustables selon les saisons (été/hiver).

2. **Contrôle du pH et de la Conductivité** :
   - Surveillance de l’acidité (pH) pour maintenir la stabilité chimique.
   - Suivi de la conductivité pour calculer la salinité et la densité de l’eau.

3. **Gestion Automatique des Niveaux d’Eau** :
   - Détection des niveaux d’eau haut et bas avec des capteurs optiques.
   - Activation automatique d’une électrovanne pour compenser l’évaporation.
   - Mesure des volumes d’eau ajoutés avec un capteur de débit, incluant des alertes pour anomalies.

4. **Mises à Jour OTA (Over-The-Air)** :
   - Flash des firmwares à distance via Wi-Fi.
   - Vérification de l’intégrité des fichiers avec des signatures MD5.

---

### **Objectifs du Guide**
Ce guide se concentre exclusivement sur la **configuration ESPHome**, en détaillant :
- L’installation d’ESPHome.
- La préparation et le téléchargement des fichiers YAML.
- Le flash initial du firmware et la configuration Wi-Fi.
- L’intégration avec Home Assistant pour le contrôle et la surveillance.

⚠️ Un second guide traitera de la **configuration matérielle** : câblage des capteurs, alimentation, et mise en place dans l’aquarium.

---

### **Avantages du Projet**
- **Automatisation** : Réduit les tâches manuelles telles que la gestion des niveaux d’eau.
- **Sécurité** : Notifications et diagnostics en cas de conditions dangereuses.
- **Flexibilité** : Ajout simplifié de nouveaux capteurs ou fonctions.
- **Intégration** : Compatible avec Home Assistant pour un contrôle centralisé.

## **Prérequis**

Avant de commencer l’installation d’Aquarium Monitor avec ESPHome, assurez-vous de disposer des éléments suivants.

---

### **Matériel Requis**
1. **Microcontrôleur** :
   - **ESP32** : Recommandé pour ses performances et ses nombreux GPIO disponibles.
2. **Câble USB** :
   - Nécessaire pour flasher le firmware initial.
3. **Capteurs et Actionneurs** :
   - **Sondes de Température Dallas DS18B20** :
     - Connectées via le bus 1-Wire pour mesurer la température de l’eau.
   - **Circuits EZO (Atlas Scientific)** :
     Ces modules permettent des mesures précises de paramètres chimiques :
     - [Circuit pH](https://atlas-scientific.com/ph/original-ph-circuit/) : Surveillance et calibration du pH.
     - [Circuit Conductivité (EC)](https://atlas-scientific.com/conductivity/original-ec-circuit/) : Pour calculer la salinité et la densité.
     - [Circuit ORP](https://atlas-scientific.com/oxidation-reduction-potential/original-orp-circuit/) : Mesure du potentiel d’oxydoréduction.
     - (Optionnel) [Circuit RTD](https://atlas-scientific.com/temperature/rtd-temperature-circuit/) : Mesure avancée de la température.
     - (Optionnel) [Circuit DO](https://atlas-scientific.com/dissolved-oxygen/original-do-circuit/) : Pour l'oxygène dissous.
   - **Capteurs Optiques** :
     - Pour détecter les niveaux d’eau (haut/bas).
   - **Électrovanne** :
     - Contrôlée automatiquement pour compenser l’évaporation.
   - **(Optionnel) Capteur de Débit** :
     - Permet de mesurer les volumes d’eau ajoutés.

---

### **Logiciels Requis**
1. **ESPHome** : 
   - **Via Home Assistant** (recommandé) :
     - Installez l’add-on ESPHome dans l’onglet Supervisor de Home Assistant.
   - **Installation locale avec Python** (alternative) :
     - Téléchargez ESPHome depuis [https://esphome.io/](https://esphome.io/).

2. **Python** (si installation locale) :
   - Téléchargez la dernière version sur [Python.org](https://www.python.org/downloads/).

3. **Accès GitHub** :
   - Clonez ou téléchargez les fichiers YAML nécessaires depuis le dépôt :
     [Aquarium Monitor - GitHub](https://github.com/twinsen68/reefmonitor).

4. **Utilisation du fichier `install.yaml`** :
   - Si vous utilisez ESPHome via Home Assistant, le fichier `install.yaml` permet de récupérer automatiquement tous les fichiers nécessaires. Pour l’utiliser :
     - Placez le fichier `install.yaml` dans l’interface ESPHome.
     - Lancez la commande de création du nœud (détails dans la section suivante).

---

### **Informations Réseau**
- Préparez les informations suivantes avant de configurer l’ESP :
  - **SSID** : Nom de votre réseau Wi-Fi.
  - **Mot de passe Wi-Fi** : Clé d’accès au réseau.
- Vérifiez que votre réseau Wi-Fi fonctionne en 2,4 GHz, car les ESP32 ne supportent pas le 5 GHz.

---

### **Configuration Minimale Requise**
- **ESP32** avec au moins 4 Mo de mémoire flash.
- Un ordinateur ou serveur pour configurer ESPHome.
- Une connexion réseau stable pour les mises à jour et l’intégration.

---

### **Résumé des Prérequis**
1. Procurez-vous le matériel et les capteurs listés, notamment les circuits EZO d’Atlas Scientific.
2. Installez ESPHome via Home Assistant ou localement avec Python.
3. Préparez vos informations réseau et familiarisez-vous avec le fichier `install.yaml` pour simplifier la configuration.

Une fois ces éléments prêts, passez à l’étape suivante : **Installation d’un Nœud ESPHome**.
## **Installation d’un Nœud ESPHome**

### **Étape 1 : Récupération des Fichiers**
1. Téléchargez les fichiers nécessaires depuis le dépôt GitHub :
   - [Aquarium Monitor - GitHub](https://github.com/twinsen68/reefmonitor).
   - Assurez-vous de disposer du fichier `install.yaml` pour automatiser l’installation via Home Assistant.
2. Placez les fichiers YAML dans un répertoire accessible par ESPHome.

---

### **Étape 2 : Création d’un Nœud dans ESPHome**

#### **Si vous utilisez Home Assistant :**
1. **Ouvrez l’interface ESPHome** via l’add-on installé dans Home Assistant.
2. Cliquez sur **"+"** pour ajouter un nouveau nœud.
3. Dans la fenêtre de création :
   - **Nom** : Saisissez `reefmonitor` ou un autre nom unique.
   - **Type d'appareil** : Sélectionnez **ESP32**.
4. Une fois le nœud créé, ouvrez le fichier YAML associé et remplacez tout son contenu par celui de `install.yaml`.

5. Sauvegardez et cliquez sur **INSTALLER**.
   - Sélectionnez **"Connecter au port USB"** pour le premier flash.
   - Branchez l’ESP32 à votre ordinateur via USB.

---

#### **Si vous utilisez ESPHome en local :**
1. **Placez `install.yaml`** dans le dossier où ESPHome est configuré.
2. Ouvrez un terminal et exécutez la commande suivante pour compiler et flasher le firmware initial :
   ```bash
   esphome run install.yaml
   ```

### **Procédure de flash depuis le terminal macOS**

Pour les projets volumineux ou lorsque l’interface graphique de Home Assistant n’est pas adaptée, vous pouvez flasher l’ESP32 directement depuis un Mac. Voici une procédure complète :

1. **Installer Python 3 et ESPHome**
   - macOS inclut Python, mais il est recommandé d’installer une version à jour :
     ```bash
     brew install python@3
     ```
   - Installez ensuite ESPHome via `pipx` (recommandé) ou `pip` :
     ```bash
     python3 -m pip install --user pipx
     python3 -m pipx install esphome
     ```
     > 💡 Vous pouvez utiliser `pip install --user esphome` si `pipx` n’est pas disponible.

2. **Connecter l’ESP32 au Mac**
   - Branchez l’ESP32 via un câble USB.
   - Identifiez le port série attribué :
     ```bash
     ls /dev/cu.*
     ```
     Les ports des ESP32 apparaissent généralement sous la forme `/dev/cu.SLAB_USBtoUART` ou `/dev/cu.usbserial-xxxx`.

3. **(Optionnel) Donner les droits d’accès au port**
   - Si une erreur de permission survient, ajustez les droits :
     ```bash
     sudo chmod a+rw /dev/cu.SLAB_USBtoUART
     ```

4. **Se placer dans le dossier du projet**
   - Clonez le dépôt puis entrez dans le dossier :
     ```bash
     git clone https://github.com/twinsen68/reefmonitor.git
     cd reefmonitor
     ```
   - Assurez-vous que le fichier `install.yaml` est bien présent dans ce dossier.

5. **Compiler et flasher le firmware**
   - Placez-vous dans le dossier contenant `install.yaml` (ou le fichier YAML de votre nœud).
   - Lancez la commande en précisant le port série :
     ```bash
     esphome run install.yaml --device /dev/cu.SLAB_USBtoUART
     ```
     - La compilation peut prendre plusieurs minutes selon la taille du projet.
     - Lors de l’étape de flash, maintenez éventuellement le bouton **BOOT** de l’ESP32 si la connexion échoue au premier essai.

6. **Vérifier le flash et passer aux mises à jour OTA**
   - Une fois le flash terminé, l’ESP32 redémarre et se connecte au Wi-Fi configuré.
   - Vous pouvez ensuite effectuer toutes les mises à jour suivantes via **OTA**, soit depuis Home Assistant, soit avec :
     ```bash
     esphome run install.yaml --device OTA
     ```
   - Pour effacer complètement la mémoire flash avant un nouveau déploiement, utilisez :
     ```bash
     esptool.py --port /dev/cu.SLAB_USBtoUART erase_flash
     ```

### **Étape 3 : Configuration Wi-Fi**
### **Étape 3 : Configuration Wi-Fi**

#### **Utilisation via Home Assistant :**
1. La configuration Wi-Fi est automatiquement gérée grâce au fichier `secrets.yaml` dans Home Assistant :
   - Les champs suivants doivent être définis dans le fichier `secrets.yaml` de Home Assistant :
     ```yaml
     wifi_ssid: "Votre_Nom_De_Réseau_Wi-Fi"
     wifi_password: "Votre_Mot_De_Passe_Wi-Fi"
     ```
   - Le fichier YAML du projet (comme `install.yaml`) inclut cette configuration Wi-Fi en utilisant les références :
     ```yaml
     wifi:
       ssid: !secret wifi_ssid
       password: !secret wifi_password
     ```
   - Il n’est donc pas nécessaire de configurer manuellement le Wi-Fi après le flash initial.

2. Après le flash USB via Home Assistant :
   - Le nœud ESP se connecte automatiquement au réseau défini dans les secrets Wi-Fi.
   - Passez directement à l’étape 4 pour vérifier le téléchargement des fichiers restants.

---

#### **Utilisation en Local ou Sans Home Assistant :**
1. Si vous utilisez ESPHome localement ou sans fichier `secrets.yaml` :
   - Après le flash initial, l’ESP32 redémarre en mode **point d’accès Wi-Fi**.
   - Connectez-vous au réseau créé par l’ESP :
     - **Nom du réseau** : `reefmonitor-XXXX` (où `XXXX` est une partie de l’adresse MAC).
     - **Mot de passe** : `configme`.
2. Une fois connecté, une interface web s’ouvre automatiquement pour la configuration :
   - Entrez le **SSID** (nom de votre réseau Wi-Fi).
   - Entrez le **mot de passe Wi-Fi**.
3. Validez les informations. L’ESP redémarre et se connecte à votre réseau domestique.

---

### **Étape 4 : Téléchargement des Fichiers Restants**
1. Une fois que l’ESP32 est connecté à votre réseau Wi-Fi :
   - ESPHome utilise le fichier `install.yaml` pour récupérer automatiquement tous les fichiers YAML nécessaires au projet.
2. Vérifiez les **logs ESPHome** pour confirmer que les fichiers sont correctement téléchargés :
   - Dans l’interface ESPHome, cliquez sur le nom de votre nœud (`reefmonitor`).
   - Regardez les logs pour vous assurer que les capteurs et les fichiers sont correctement chargés.

---

### **Étape 5 : Vérification**
1. Depuis l’interface ESPHome, ouvrez les **logs** de votre nœud (`reefmonitor`) :
   - Vérifiez que tous les capteurs sont détectés.
   - Assurez-vous que les données des capteurs (température, pH, conductivité, etc.) s’affichent correctement.
2. Si tout est en ordre, l’installation du nœud est terminée !

---
### **Étape 6 : Mise à Jour du Firmware OTA**

Une fois le nœud configuré et fonctionnel, vous pouvez mettre à jour son firmware sans avoir besoin de rebrancher l’ESP à votre ordinateur.

#### **Mise à Jour via Home Assistant :**
1. Dans l’interface ESPHome, cliquez sur votre nœud (`reefmonitor`).
2. Cliquez sur **INSTALLER** et choisissez **"Sans fil (OTA)"** :
   - ESPHome compile et télécharge automatiquement le firmware mis à jour sur le nœud.

#### **Mise à Jour en Local :**
1. Exécutez la commande suivante dans votre terminal :
   ```bash
   esphome run reefmonitor.yaml

## **Configuration des Capteurs et Actionneurs**

Une fois votre nœud ESP configuré et connecté, il est temps de paramétrer les capteurs et actionneurs. Les fichiers YAML fournis contiennent déjà une configuration de base. Cependant, certaines sections peuvent nécessiter une personnalisation selon votre matériel ou vos besoins.

---

### **Capteurs et Actionneurs Prévus**

#### **1. Température (Dallas DS18B20)**
- **Rôle** : Mesure la température de l'eau avec précision.
- **Configuration YAML** : Le capteur est configuré dans le fichier `temperature_dallas.yaml` :
  ```yaml
  sensor:
    - platform: dallas
      address: 0x3D0000001CDEF
      name: "Température Aquarium"
      unit_of_measurement: "°C"
      update_interval: 60s

- **Personnalisation** :
Adresse : Modifiez l’adresse du capteur Dallas en fonction de votre matériel.
Scannez les capteurs connectés via les logs ESPHome pour identifier leurs adresses.
Intervalle : Ajustez update_interval (par défaut à 60s).

#### **2. pH (Circuit EZO)**

- **Rôle** : Surveille l’acidité de l’eau.
- **Configuration YAML**: Le capteur est configuré dans le fichier ezo_ph.yaml :
  ```yaml
  sensor:
    - platform: ezo
      name: "pH Aquarium"
      address: 99
      update_interval: 60s
- **Personnalisation** :
Adresse I2C : Vérifiez que l’adresse du circuit correspond (par défaut : 99).
Calibration : Suivez les étapes décrites dans le fichier YAML pour calibrer le pH avec des solutions de référence (pH 4, 7, 10).

#### **3. Conductivité (Circuit EZO)**

- **Rôle** : Permet d’estimer la salinité et la densité de l’eau.
- **Configuration YAML** : Le capteur est configuré dans le fichier ezo_ec.yaml :
  ```yaml
  sensor:
    - platform: ezo
      name: "Conductivité Aquarium"
      address: 100
      update_interval: 60s
- **Personnalisation** :
Adresse I2C : Modifiez si nécessaire (par défaut : 100).
Calibration : Calibrez avec des solutions de conductivité (exemple : 12880 µS/cm).

#### **4. ORP (Circuit EZO)**
- **Rôle** : Mesure le potentiel redox (oxydoréduction) pour évaluer la qualité chimique de l’eau.
- **Configuration YAML** : Le capteur est configuré dans le fichier ezo_orp.yaml :
  ```yaml
  sensor:
    - platform: ezo
      name: "ORP Aquarium"
      address: 98
      update_interval: 60s
- **Personnalisation** :
Adresse I2C : Vérifiez l’adresse (par défaut : 98).
#### **5. Niveaux d’Eau (Capteurs Optiques)**

- **Rôle** : Détecte les niveaux d'eau pour gérer la compensation d’évaporation.
- **Configuration YAML** : Les capteurs sont configurés dans le fichier optical.yaml :
  ```yaml
  binary_sensor:
    - platform: gpio
      pin: 32
      name: "Niveau Bas"
    - platform: gpio
      pin: 33
      name: "Niveau Haut"
- **Personnalisation** :
Pins GPIO : Vérifiez que les GPIO configurés correspondent à vos connexions physiques.
#### **6. Électrovanne**

- **Rôle** : Ajoute automatiquement de l’eau pour compenser l’évaporation.
- **Configuration YAML** : L’électrovanne est contrôlée via un switch :
  ```yaml
  switch:
    - platform: gpio
      pin: 25
      name: "Électrovanne"
- **Personnalisation** :
GPIO : Ajustez selon le pin connecté à la vanne.

## **Intégration dans Home Assistant**

Une fois les capteurs et actionneurs configurés et fonctionnels sur votre nœud ESP, vous pouvez les intégrer dans Home Assistant pour un contrôle et une surveillance centralisés.

---

### **1. Détection Automatique du Nœud**
Home Assistant détecte automatiquement les nœuds ESPHome sur le réseau local. Voici comment ajouter votre nœud :
1. Accédez à l’interface de Home Assistant.
2. Cliquez sur **Paramètres** > **Appareils et Services**.
3. Dans la section **Découvertes**, votre nœud ESP apparaîtra sous le nom configuré (par défaut : `reefmonitor`).
4. Cliquez sur **Configurer**, puis suivez les instructions pour ajouter le nœud.

---

### **2. Vérification des Entités**
1. Une fois le nœud ajouté, Home Assistant crée automatiquement les entités correspondant aux capteurs et actionneurs configurés dans vos fichiers YAML.
2. Accédez à **Paramètres** > **Appareils et Services**, puis recherchez votre nœud pour afficher les entités associées.
   - Exemple d'entités :
     - `sensor.temperature_aquarium` : Température de l’eau.
     - `sensor.ph_aquarium` : pH.
     - `binary_sensor.niveau_bas` : Niveau d’eau bas détecté.
     - `switch.electrovanne` : Contrôle manuel de l’électrovanne...

### **3. Configuration des Alertes**
Vous pouvez configurer des automatisations ou des notifications pour alerter en cas de dépassement des plages critiques. Voici un exemple d’automatisation pour la température :
```yaml
alias: Alerte Température
description: Alerte si la température dépasse les limites.
trigger:
  - platform: numeric_state
    entity_id: sensor.temperature_aquarium
    above: 28  # Limite haute.
  - platform: numeric_state
    entity_id: sensor.temperature_aquarium
    below: 22  # Limite basse.
action:
  - service: notify.mobile_app
    data:
      message: "Alerte : Température critique détectée dans l'aquarium !

### **2. Vérification des Entités**
