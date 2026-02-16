# API-FERM – Manual de Consumo (FERM Registry)

Backend service that enables national restoration-monitoring platforms to connect their data with the **FAO Forest and Landscape Restoration Monitoring (FERM) system**.

## Links
- **Admin portal (API Key + Swagger UI):** https://ferm.fao.org/admin/api  
- **API Base URL:** https://ferm.fao.org/api

## What you can do with this API
- Generate an **API Key** (via the admin portal)
- Verify service availability (**Health**)
- Create and retrieve **restoration projects** (GeoJSON FeatureCollection payload)
- Download **sample payloads** (JSON/GeoJSON)

## Authentication
All requests must include:

- Header: `x-api-key: <YOUR_API_KEY>`

> Note: Only **one API Key can be active per user**. Creating a new key revokes the previous one.

## Quickstart (Smoke test)
### Health check
```bash
curl -X GET "https://ferm.fao.org/health"   -H "x-api-key: <YOUR_API_KEY>"
```

### Detailed health check
```bash
curl -X GET "https://ferm.fao.org/health/detailed"   -H "x-api-key: <YOUR_API_KEY>"
```

## Endpoints
### Health
- `GET /health`
- `GET /health/detailed`

### Projects
- `POST /api/projects` — Create a new restoration project
- `GET /api/projects/{id}` — Get project by ID

### Samples
- `GET /api/samples` — List published sample payloads (JSON/GeoJSON)

## Create a Project (example)
The API expects a **GeoJSON FeatureCollection**:
- `properties`: project metadata (institutionId, title, years, contacts, country, etc.)
- `features[]`: sites/areas (Polygon geometry recommended)

```bash
curl -X POST "https://ferm.fao.org/api/projects"   -H "x-api-key: <YOUR_API_KEY>"   -H "Content-Type: application/json"   --data-binary @- <<'GEOJSON'
{
  "type": "FeatureCollection",
  "properties": {
    "institutionId": "ORG_SAMPLE_001",
    "title": "Demo Restoration Project (Basic)",
    "startingYear": 2026,
    "restorationTypes": [1],
    "pointsOfContact": [
      { "name": "Alex Rivera", "email": "alex.rivera@example.org" }
    ],
    "organizations": [
      {
        "name": "Restoration Demo Org",
        "role": "Lead implementer",
        "type": "NGO",
        "country": "PA"
      }
    ],
    "country": "PA",
    "countries": ["PA"]
  },
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Polygon",
        "coordinates": [[
          [-79.6, 8.9],
          [-79.6, 9.0],
          [-79.5, 9.0],
          [-79.5, 8.9],
          [-79.6, 8.9]
        ]]
      },
      "properties": {
        "siteName": "Demo Site A",
        "area": 25.0
      }
    }
  ]
}
GEOJSON
```

## Get a Project by ID
```bash
curl -X GET "https://ferm.fao.org/api/projects/<PROJECT_ID>"   -H "x-api-key: <YOUR_API_KEY>"
```

## Documentation (User Guide)
- 📘 **User Manual (DOCX):** `docs/manual-api/Guia_Usuario_API_FERM_Panama.docx`
- 📄 Recommended: keep a readable version in Markdown at `docs/manual-api/README.md`

## Suggested repository structure
```text
docs/
  manual-api/
    README.md
    Guia_Usuario_API_FERM_Panama.docx
```

## Operational recommendations
- Store the API Key in a **secrets manager** (never hardcode)
- Start with a **small dataset** (1–10 projects), then scale up
- Keep a local log of request/response (timestamp, payload hash, response, projectId)
- Automate with a scheduler (cron/job) once E2E tests are validated

## Troubleshooting
- **401/403:** missing or invalid `x-api-key`
- **400:** invalid payload (check GeoJSON structure and required fields)
- **Old key stopped working:** a new key may have been generated (only one active)

---

Maintained by: **Ing. Aida Zapata Orozco**  
Contact: [add email / organization]
