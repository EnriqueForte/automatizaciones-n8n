# 🛡️ IP Reputation Tracker (n8n + AbuseIPDB)

Automatización en **n8n** para consultar reputación de IPs usando **AbuseIPDB**, registrar histórico en **Google Sheets**, enviar **alertas por Telegram** si supera un umbral de riesgo, y mandar un **informe HTML diario** por Gmail.

> Proyecto orientado a SOC/Blue Team y OSINT para enriquecer IOCs y tener visibilidad rápida de IPs reportadas.

---

## ✨ Funcionalidades
- 🧾 *Input* de IPs desde Google Sheets.
- 🕵️ Consulta de reputación vía **AbuseIPDB** (ventana 90 días, modo `verbose`).
- 🚨 Alerta inmediata a **Telegram** si `abuseConfidenceScore > 50`.
- 📚 Registro histórico en Google Sheets (con columna clave `day_key` para deduplicar por día).
- 📨 Resumen diario en **HTML** por Gmail (tabla con Score, Reports, País, ISP, Dominio y último reporte).

## 🗂️ Estructura
```
ip-reputation-tracker-n8n/
├─ workflow/
│  └─ ip-reputation-tracker.json   # Workflow n8n (sanitizado, sin secretos)
├─ docs/
│  └─ images/
│     ├─ workflow_overview.png
│     ├─ telegram_alert.png
│     ├─ report_html.png
│     ├─ sheets_IPs.png
│     └─ sheets_log.png
├─ .env.example
├─ .gitignore
├─ LICENSE
├─ SECURITY.md
└─ README.md
```

## ⚙️ Requisitos
- n8n (self-hosted o Cloud)
- Cuenta en **AbuseIPDB** (API key)
- **Google Sheets** para la hoja de entrada y el histórico
- **Telegram** (bot + chat ID) para alertas
- **Gmail** OAuth2 en n8n para enviar el informe

## 🚀 Puesta en marcha (n8n)
1. **Clona** este repo y entra en la carpeta:
   ```bash
   git clone https://github.com/EnriqueForte/ip-reputation-tracker-n8n.git
   cd ip-reputation-tracker-n8n
   ```
2. Copia `.env.example` a `.env` y completa las variables:
   ```bash
   cp .env.example .env
   # Edita ABUSEIPDB_API_KEY, TELEGRAM_CHAT_ID, REPORT_EMAIL
   ```
3. En **n8n**: *Import Workflow* → selecciona `workflow/ip-reputation-tracker.json`.
4. Asocia las **credenciales** en los nodos (HTTP Request / Telegram / Google Sheets / Gmail).
5. En los nodos de **Google Sheets**, sustituye los *Document IDs* por:
   - `<GOOGLE_SHEET_ID_INPUTS>`: hoja con IPs (columna A).
   - `<GOOGLE_SHEET_ID_LOG>`: hoja para el histórico (A:K con cabeceras).
6. Ejecuta manualmente para validar. Si todo va bien, añade un **Schedule Trigger** para periodicidad (p.ej. cada hora).

## 🧩 Formato de las Hojas de Google
- **IPs para Escanear** (entrada):
  - Columna **A**: `IP` (una por fila)
- **AbuseIPDB_Log** (histórico, ejemplo de cabeceras):
  - `timestamp, ip, abuseConfidenceScore, totalReports, lastReportedAt, countryCode, isp, domain, usageType, isWhitelisted, day_key`

> El workflow genera `day_key` = `IP_YYYY-MM-DD` para evitar duplicados diarios.

## 🔒 Variables y secretos
Este repositorio **no** incluye claves. El workflow usa expresiones de n8n para leer:
- `{$env.ABUSEIPDB_API_KEY}`
- `{$env.TELEGRAM_CHAT_ID}`
- `{$env.REPORT_EMAIL}`

Guárdalas en el entorno de n8n o en un `.env` administrado por tu despliegue.

## 🧱 Arquitectura (alto nivel)
```
Google Sheets (IPs) → Parse & Normalize → HTTP AbuseIPDB → IF(Score>50) → Telegram
                                           ↓
                                     Merge → Map → day_key → Google Sheets (Histórico)
Histórico (últimos 5 min) → Aggregate → Resumen (HTML) → Gmail (Informe diario)
```

## 📸 Capturas sugeridas
- Vista del workflow en n8n con los nodos conectados.
- Ejemplo de alerta de Telegram.
- Fragmento del informe HTML recibido por correo.
- Hoja de Google con el histórico.
- Hoja de Google para subir Ips.

## 🧰 Troubleshooting
- **403 / 429 en AbuseIPDB**: revisa API key / rate limit del plan.
- **No llegan alertas Telegram**: verifica `TELEGRAM_CHAT_ID` y que el bot tenga permiso en el chat.
- **Gmail bloquea envío**: revisa credenciales OAuth2 y permisos en n8n.
- **No escribe en Sheets**: comprueba el *Document ID*, rango y credenciales.

## 📝 Licencia
[MIT](./LICENSE)

---

**Autor:** Enrique Forte · *Proyecto Blue Team / OSINT para portfolio*  
¿Ideas o mejoras? ¡Pull Requests bienvenidos!
