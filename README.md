# ChainAuth Worker

Playwright/Chromium worker voor GRANTLY ketenmachtiging headless login-tests.


## Geen lokale worker meer nodig

Deze worker is bedoeld om via GitHub automatisch naar Cloud Run of Render te deployen. GRANTLY hoeft dan alleen de publieke worker-URL te gebruiken; je hoeft lokaal geen `npm run start` meer te draaien.

### GitHub → Google Cloud Run

1. Zet deze map `chainauth-worker` in een GitHub repository.
2. Voeg GitHub secrets toe:
   - `GCP_SERVICE_ACCOUNT_JSON`
   - `WORKER_TOKEN`
3. Push naar `main`.
4. GitHub Actions deployt automatisch naar Cloud Run.
5. Kopieer de Cloud Run URL naar GRANTLY → ChainAuth → Provider settings → Worker URL.

### Render alternatief

De meegeleverde `render.yaml` kan direct als Render Blueprint gebruikt worden. Render start de Docker worker automatisch en geeft een publieke URL terug.


## Lokaal draaien

### Snelste manier

macOS/Linux:

```bash
./start-local.sh
```

Windows:

```bat
start-local.bat
```

De worker leest automatisch `.env`, installeert dependencies wanneer `node_modules` ontbreekt en start standaard op `http://127.0.0.1:8080`.

### Via npm

```bash
npm install
npm run start:local
```

### Via Docker Compose

```bash
docker compose up -d --build
```

### Via Docker handmatig

```bash
docker build -t chainauth-worker .
docker run --rm -p 8080:8080 -e WORKER_TOKEN=change-me chainauth-worker
```

Healthcheck:

```bash
curl http://127.0.0.1:8080/health
```

Login-test:

```bash
curl -X POST http://127.0.0.1:8080/login/zlogin \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer change-me' \
  -d '{
    "username": "demo",
    "password": "secret",
    "start_url": "https://zlogin.nl/",
    "timeout_ms": 30000
  }'
```

## GRANTLY instellingen

Vul in de module settings:

- Worker URL: standaard `http://127.0.0.1:8080` lokaal, `http://host.docker.internal:8080` vanuit een Docker-container, of je publieke Cloud Run/Kubernetes URL
- Worker token: dezelfde waarde als `WORKER_TOKEN`
