# Arquitectura (alto nivel)

```mermaid
flowchart LR
  A[Cliente / Fuente] -- POST JSON --> B[Webhook n8n]
  B --> C[Normaliza IPs / Deduplica]
  C --> D[Split in Batches]
  D --> E[HTTP Request → VirusTotal]
  E --> F[Parser de respuesta]
  F -->|riesgo| G[IF: Alertar]
  G --> H[Telegram (mensaje)]
  F --> I[Google Sheets (registro)]
```