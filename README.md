# 🚨 n8n – VirusTotal IP Checker (Webhook → VT → Alerting)

Automatización n8n para **analizar IPs contra VirusTotal** y **enviar alertas** cuando se detecten indicadores de riesgo. Incluye **registro histórico** en Google Sheets y notificación por **Telegram**.

> **Nota de seguridad:** Este repositorio **no** contiene secretos. Las claves y IDs deben configurarse en **credenciales/variables de n8n** o en tu entorno local. Sustituye los placeholders por tus valores.

---

## 🧩 ¿Qué hace?

1. **Entrada (Webhook):** recibe una o varias IPs (`["1.2.3.4","8.8.8.8"]` o cadena separada por comas/espacios).
2. **Consulta VirusTotal (HTTP):** `GET /api/v3/ip_addresses/{ip}`.
3. **Parsing:** resume `malicious`, `suspicious`, `harmless`, `undetected`, `reputation`, `ASN`, `país`.
4. **Decisión (IF):** si hay riesgo → alerta.
5. **Salida:**
   - **Telegram:** mensaje con resumen y enlace directo al informe VT.
   - **Google Sheets:** registro con `timestamp`, IP y métricas, usando `day_key` = `IP_YYYY-MM-DD` para evitar duplicados diarios.

El flujo está en `workflows/VirusTotal_IP_Checker.json`. Documentación adicional en `docs/`.

---

## 🗂 Estructura

```
workflows/
  └─ VirusTotal_IP_Checker.json
docs/
  └─ architecture.md
img/
  ├─ Comando PowerShell.png      
  └─ Diagrama Flujo luego de Ejecucion.png
  └─ Diagrama Flujo.png
  └─ Exportacion a GoogleSheet.png
  └─ Mensajes enviados a Telegram Alertas.png  
.env.example
.gitattributes
.gitignore
LICENSE
README.md
```

---

## ⚙️ Requisitos

- **n8n** (Cloud o self-hosted).
- Credenciales:
  - **VirusTotal API Key**.
  - **Telegram** (bot token + chatId) si usas alertas.
  - **Google Sheets** (OAuth) si usas el histórico.

---

## 🔐 Configuración de secretos

Configura **credenciales de n8n** y/o variables de entorno. Este repo provee `.env.example` únicamente como guía:

```bash
VT_API_KEY=YOUR_VT_API_KEY
TELEGRAM_CHAT_ID=YOUR_TELEGRAM_CHAT_ID
GSHEET_ID=YOUR_GOOGLE_SHEET_ID
```

En el JSON de ejemplo no hay claves reales. Si importas un flujo propio, **no** subas claves al repositorio.

---

## ▶️ Uso

### 1) Importa el workflow
- En n8n: **Import from File** → `workflows/VirusTotal_IP_Checker.json`.

### 2) Configura credenciales
- **HTTP Request (VirusTotal):** mueve `x-apikey` a una credencial/variable segura.
- **Telegram:** selecciona tu credencial del bot y el `chatId`.
- **Google Sheets:** selecciona cuenta y `documentId`/`sheetName`.

### 3) Endpoint de prueba
- Webhook (POST) con payload JSON:
```json
{ "ips": ["1.2.3.4", "8.8.8.8"] }
```
o
```json
{ "ips": "1.2.3.4, 8.8.8.8" }
```

### 4) Resultado
- Si la IP tiene señales de riesgo → recibirás un mensaje en **Telegram** con resumen y enlace a VT.
- Todas las IPs se registran en **Google Sheets** (si activado).

---

## 🧠 Detalles técnicos

- **Normalización de IPs** (limpieza y deduplicado).
- **Split in Batches** para procesar múltiples IPs de forma segura.
- **Clave de unicidad** en Sheets: `day_key = IP_YYYY-MM-DD`.
- **Mensajes de alerta** con resumen compacto (VT stats, reputación, ASN/ASOwner, país) y enlace directo al informe.

> Nodos destacados: Webhook → Validate & Prepare → Split in Batches → HTTP Request (VT) → Parse → IF → Telegram/Sheets.

---

## 📈 Mejoras sugeridas

- **Backoff** ante rate limits de VT (retry con espera exponencial).
- **Soporte IPv6**.
- **Etiquetado por severidad** (umbral de `malicious`/`suspicious`).
- **Dashboard** (Sheets/Looker Studio o Grafana) para tendencias.
- **SECURITY.md** para recepción responsable de vulnerabilidades.

---

## 🖼 Imágenes y diagramas

- **Incluye capturas** del workflow y un **diagrama** simple del flujo de datos.

---

## 📄 Licencia

Este proyecto se distribuye bajo licencia **MIT** (ver `LICENSE`).

---

## 🤝 Contribuciones

Sugerencias y PRs son bienvenidos. Por favor, evita subir información sensible y respeta la guía de seguridad.
