# README

[![Release](https://img.shields.io/docker/v/jcpd/dknr-zendure?sort=semver)](https://img.shields.io/docker/v/jcpd/dknr-zendure?sort=semver)
[![Pull](https://img.shields.io/docker/pulls/jcpd/dknr-zendure)](https://img.shields.io/docker/pulls/jcpd/dknr-zendure)
[![Issue](https://img.shields.io/github/issues/jcdenis/dknr-zendure)](https://img.shields.io/github/issues/jcdenis/dknr-zendure)
[![License](https://img.shields.io/github/license/jcdenis/dknr-zendure)](https://github.com/JcDenis/dknr-zendure/blob/master/LICENSE)


## A propos

**dknr-zendure** est un projet consacré à la récupération et à l'exploitation des données des onduleurs et batteries Zendure (Hyper 2000) via l'API développeur MQTT de Zendure.


## Fonctionnalités

- **Suivi des onduleurs et des batteries** : niveau de charge, puissances, températures, état de fonctionnement.
- **Support du compteur Zendure 3CT** via un broker MQTT.
- **Intégration possible** dans une installation domotique ou un outil de supervision.
- **Support Home-Assistant Discovery** pour les messages MQTT.
- **Historique de charge et décharge des batteries** enregistré dans InfluxDB : énergie par heure, par jour, par mois et par an.


## Matériel requis

* un onduleur Zendure (testé avec un **Hyper 2000**) et ses batteries, associés à un compte Zendure.
* une machine exécutant le logiciel docker et connectée à internet.


## Installation

Des images Docker prêtes à l'emploi sont disponibles. 
Utilisez le fichier docker-compose.yaml à la racine du dépôt en fournissant les variables d'environnement suivantes :

* **DKNR_USERNAME** : login de l'interface node-red.
* **DKNR_PASSWORD** : mot de passe de l'interface node-red.
* **DKNR_SECRET**   : un mot secret pour encrypter les credits.

Ajoutez ensuite votre onduleur depuis la page **Ajouter** avec l'adresse mail de votre compte Zendure et le numéro de série de l'onduleur.

### Historique (charge et décharge des batteries, InfluxDB)

Le fichier docker-compose démarre aussi un conteneur **InfluxDB 2** (`dknr_zendure_influxdb`) qui conserve les valeurs relevées sans limite de durée. La page **Historique** du tableau de bord affiche l'énergie chargée (au-dessus de l'axe) et déchargée (en dessous de l'axe) par les batteries par heure (24 dernières heures), par jour (7 derniers jours), par mois (12 derniers mois) et par an.

Aucune variable supplémentaire n'est obligatoire : par défaut, dknr-zendure utilise le conteneur InfluxDB du docker-compose. Les variables suivantes sont optionnelles :

| Variable | Valeur par défaut | Rôle |
|---|---|---|
| **DKNR_INFLUX_TOKEN** | `dknr-zendure-influxdb-token` | jeton d'API InfluxDB |
| **DKNR_INFLUX_URL** | `http://dknr_zendure_influxdb:8086` | adresse de la base InfluxDB |
| **DKNR_INFLUX_ORG** | `dknr` | organisation InfluxDB |
| **DKNR_INFLUX_BUCKET** | `zendure` | bucket InfluxDB |

Au premier démarrage, InfluxDB est initialisé avec l'utilisateur **DKNR_USERNAME** et le mot de passe **DKNR_PASSWORD**, qui doit faire **8 caractères minimum**.

Quelques remarques :

* Le jeton par défaut convient tant qu'InfluxDB reste sur le réseau interne de docker. Si vous exposez le port 8086 (lignes commentées dans le docker-compose), définissez votre propre **DKNR_INFLUX_TOKEN**, par exemple généré avec `openssl rand -hex 32`.
* Le jeton, l'organisation et le bucket ne sont appliqués par InfluxDB qu'à sa **première** initialisation. Pour les changer ensuite, il faut supprimer le volume `dknr_zendure_influxdb` (et donc l'historique) ou les modifier dans l'interface d'InfluxDB.
* Pour utiliser une base InfluxDB 2 existante, renseignez ces quatre variables pour le conteneur dknr_zendure ; le conteneur InfluxDB du docker-compose (et le `depends_on` qui y fait référence) peut alors être retiré.
* Zendure ne fournit que des puissances instantanées : la puissance de charge (`outputPackPower`) et de décharge (`packInputPower`) est intégrée toutes les 10 secondes dans deux compteurs en Wh, conservés au redémarrage de Node-RED. L'énergie n'est pas comptée pendant un arrêt de Node-RED ni quand l'onduleur n'envoie plus de données pendant 15 minutes.


## Contribuer

Ce projet est **open-source**, vous pouvez participer en créant des [tickets](https://github.com/JcDenis/dknr-zendure/issues) ou en proposant de [requêtes](https://github.com/JcDenis/dknr-zendure/pulls) sur le dépôt github du projet.


## Voir aussi

- [DKNR Teleinfo](https://github.com/JcDenis/dknr-teleinfo)
- [DKNR APSystems](https://github.com/JcDenis/dknr-apsystems)
- [DKNR Zendure](https://github.com/JcDenis/dknr-zendure)
- [DKNR Daikin](https://github.com/JcDenis/dknr-daikin)
