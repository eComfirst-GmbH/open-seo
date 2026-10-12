# OpenSEO Self-Host – seo.ecomfirst.de (eComfirst / Hetzner)

Fork: [eComfirst-GmbH/open-seo](https://github.com/eComfirst-GmbH/open-seo) (Upstream: [every-app/open-seo](https://github.com/every-app/open-seo)).

## Ziel

OpenSEO als Docker-Container auf dem **PipeFirst-Hetzner-Server** (gleicher Host wie `meet.ecomfirst.de`), erreichbar unter **https://seo.ecomfirst.de**, Port **3001** nur auf localhost.

## Upstream sync

```bash
git fetch upstream
git merge upstream/main   # oder: gh repo sync eComfirst-GmbH/open-seo --source every-app/open-seo
docker compose pull && docker compose up -d
```

## Quick start (Server)

```bash
git clone https://github.com/eComfirst-GmbH/open-seo.git /opt/openseo
cd /opt/openseo
cp deploy/ecomfirst/env.ecomfirst.example .env
# DATAFORSEO_API_KEY und ggf. OPENROUTER_API_KEY eintragen
docker compose -f compose.yaml -f deploy/ecomfirst/compose.ecomfirst.yaml up -d
```

Details zum App-Setup: Upstream [docs/SELF_HOSTING_DOCKER.md](../docs/SELF_HOSTING_DOCKER.md).

## Umgebungsvariablen (`.env`)

| Variable | eComfirst-Wert | Hinweis |
|----------|----------------|---------|
| `PORT` | `3001` | Nur `127.0.0.1` gebunden (siehe Compose) |
| `ALLOWED_HOST` | `seo.ecomfirst.de` | Host-Header-Schutz |
| `DATAFORSEO_API_KEY` | (Secret) | [DataForSEO](https://dataforseo.com) |
| `AUTH_MODE` | `local_noauth` | Internes Netz / vorgelagerter Schutz |
| `OPENSEO_TELEMETRY_DISABLED` | `1` | Optional, anonyme Telemetrie aus |

Optional für SAM (KI-Agent): `OPENROUTER_API_KEY`, `OPENROUTER_MODEL`.

## DNS (manuell)

A-Record: **seo.ecomfirst.de** → **89.167.108.249** (wie `meet.ecomfirst.de`, siehe PipeFirst `deploy/calcom.md`).

## Nginx (RunCloud / nginx-rc)

Snippet: [nginx-seo.ecomfirst.conf.snippet](./nginx-seo.ecomfirst.conf.snippet)

Nach Anlegen:

```bash
certbot certonly --webroot -w /var/www/certbot -d seo.ecomfirst.de
# HTTPS-Block aktivieren, nginx-rc reload
```

## Zugriffsschutz (Pflicht für internes Tool)

**Option A – Cloudflare Access** (empfohlen, analog Zero-Trust): Application für `seo.ecomfirst.de`, Policy nur `@ecomfirst.de`.

**Option B – Nginx Basic Auth** (Snippet enthält auskommentiertes Beispiel):

```bash
htpasswd -c /etc/nginx-rc/.htpasswd-seo openseo
```

Cloudflare davor: Access **oder** Basic Auth, nicht beides blind stapeln ohne Test.

## Produktion

**Noch nicht deployed** durch dieses Repo-Setup. Deploy erst nach DNS, `.env` und Freigabe durch Denis.

## PipeFirst

Keine Änderung am PipeFirst-Deploy oder an `ecomfirst.de` rsync. OpenSEO läuft als separater Stack unter `/opt/openseo`.
