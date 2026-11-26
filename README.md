# prometheus

Configuration de base d'un serveur de supervision **Prometheus** : collecte des métriques de Prometheus lui-même et d'un hôte Linux via Node Exporter, avec envoi des alertes à Alertmanager.

## Contenu

`prometheus.yml` :

| Paramètre | Valeur |
|---|---|
| Collecte et évaluation des règles | toutes les 15 s (timeout 10 s) |
| Étiquettes globales | `environment: production`, `datacenter: dc1` |
| Règles d'alerte | chargées depuis `rules/*.yml` |
| Alertmanager | `localhost:9093` |
| Cible `prometheus` | `localhost:9090` |
| Cible `node` (Node Exporter) | `localhost:9100` |

## Lancement rapide avec Docker

```bash
mkdir -p rules
docker network create monitoring
docker run -d --name node-exporter --network monitoring -p 9100:9100 prom/node-exporter
docker run -d --name prometheus --network monitoring -p 9090:9090 \
  -v "$PWD/prometheus.yml:/etc/prometheus/prometheus.yml:ro" \
  -v "$PWD/rules:/etc/prometheus/rules:ro" \
  prom/prometheus
```

Dans des conteneurs, `localhost` désigne le conteneur Prometheus lui-même : remplacez `localhost:9100` par `node-exporter:9100` (et `localhost:9093` par le nom du conteneur Alertmanager si vous en ajoutez un).

Interface web : `http://<IP>:9090` → *Status > Targets* pour vérifier que les cibles sont `UP`.

## Vérifier la configuration

```bash
promtool check config prometheus.yml
```
