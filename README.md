# 🛠️ Automatizaciones n8n para Ciberseguridad

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](./LICENSE)
![Made with n8n](https://img.shields.io/badge/Made%20with-n8n-blue)
![Status](https://img.shields.io/badge/status-active-success)
![Security First](https://img.shields.io/badge/security-first-critical)

Repositorio monorepo con **automatizaciones de ciberseguridad** construidas en **n8n** (detección, enriquecimiento de IOCs, alertas y registro). Cada proyecto vive en su propia carpeta con su **README**, **workflow(s)**, **docs** e **imágenes**.

---

## 📚 Índice

- [Proyectos incluidos](#proyectos-incluidos)
- [Estructura del repositorio](#estructura-del-repositorio)
- [Requisitos comunes](#requisitos-comunes)
- [Guía rápida](#guía-rápida)
- [Convenciones para nuevos proyectos](#convenciones-para-nuevos-proyectos)
- [Checklist de seguridad antes de subir](#checklist-de-seguridad-antes-de-subir)
- [Roadmap](#roadmap)
- [Contribuir](#contribuir)
- [Licencia](#licencia)

---

## 🚀 Proyectos incluidos

- **[VirusTotal_IP_Checker](./VirusTotal_IP_Checker/)**  
  **Descripción:** Webhook que recibe IPs, consulta **VirusTotal**, resume indicadores (malicious/suspicious/harmless/undetected, reputación, ASN/País) y lanza **alertas** (Telegram) + **registro** (Google Sheets).  
  **Carpetas:** `workflows/`, `docs/`, `img/`  
  **Uso:** Importar `workflows/VirusTotal_IP_Checker.json` en n8n, configurar credenciales y probar el webhook.
- **[IP_Reputation_Tracker](./IP_Reputation_Tracker/)**  
  **Descripción:** Toma IPs desde **Google Sheets**, consulta su reputación en **AbuseIPDB**, dispara **alertas en Telegram** si el `abuseConfidenceScore` supera el umbral, guarda **histórico en Sheets** y envía un **informe HTML** por **Gmail**.  
  **Carpetas:** `workflow/`, `docs/`, `img/`  
  **Uso:** Importar `workflow/ip_reputation_tracker.json` en n8n, asociar credenciales (HTTP/AbuseIPDB, Telegram, Google Sheets, Gmail), configurar IDs de tus Sheets y las variables de entorno `ABUSEIPDB_API_KEY`, `TELEGRAM_CHAT_ID`, `REPORT_EMAIL`.


> Este índice crecerá a medida que se añadan más automatizaciones (p.ej., URLs, dominios, hash de ficheros, feeds TI, integraciones SIEM, etc.).

---

## 🗂️ Estructura del repositorio

```
.
├─ VirusTotal_IP_Checker/         # Proyecto 1
│  ├─ workflows/                  # Workflows n8n (JSON)
│  ├─ docs/                       # Diagramas/guías técnicas
│  ├─ img/                        # Capturas enmascaradas
│  ├─ .env.example                # Variables de entorno (placeholders)
│  └─ README.md                   # README del proyecto
├─ .gitignore
├─ LICENSE
└─ README.md                      # Este archivo (índice del monorepo)
```

---

## 🧩 Requisitos comunes

- **n8n** (Cloud o self-hosted).
- **Credenciales seguras** por proyecto (p. ej., API keys, OAuth).
- **Placeholders** en los JSON exportados. Nunca subir secretos.

---

## ⚡ Guía rápida

1. **Clonar:**
   ```bash
   git clone https://github.com/EnriqueForte/automatizaciones-n8n.git
   cd automatizaciones-n8n
   ```
2. **Elegir proyecto** (ej.: VirusTotal_IP_Checker) y leer su `README.md`.
3. **Importar en n8n** → *Import from File* → `workflows/…json`.
4. **Configurar credenciales** en n8n (API keys, Telegram, Google).
5. **Probar el endpoint** (Webhook de test/producción según README del proyecto).

> Consejo: mantén **variables y credenciales** fuera del repo; usa **Credentials** de n8n o variables de entorno.

---

## 🧭 Convenciones para nuevos proyectos

- **Nombre de carpeta:** `Nombre_Proyecto` (camel/pascal con guiones bajos o kebab-case `nombre-proyecto`).
- **Estructura mínima:**
  ```
  Nuevo_Proyecto/
  ├─ workflows/
  ├─ docs/
  ├─ img/
  ├─ .env.example
  └─ README.md
  ```
- **README del proyecto:** objetivos, arquitectura, requisitos, pasos de uso, variables, endpoints de prueba, mejoras futuras.
- **Sanitización:** JSON sin secretos (placeholders p.ej. `YOUR_API_KEY`).

---

## ✅ Checklist de seguridad antes de subir

- [ ] **Sin secretos** en JSON (`x-apikey`, tokens, IDs sensibles, webhooks privados).
- [ ] `.env.example` con **placeholders** claros.
- [ ] **Capturas** en `img/` con datos **enmascarados**.
- [ ] **Licencia** compatible (MIT por defecto, o específica si aplica).
- [ ] **Notas de límites/cuotas** de APIs externas (si procede).
- [ ] (Opc.) `SECURITY.md` con guía de reporte responsable.

---

## 🗺️ Roadmap

- Nuevos “checkers”: dominios/URLs, archivos (hash), reputación ASNs, feeds TI.
- **Backoff/retries** ante *rate limits*.
- **Dashboards** (Looker Studio/Grafana) a partir de registros.
- **Plantillas** de alerting enriquecido (Markdown/HTML).
- **Pipelines** CI para validar formato de workflows antes del commit.

---

## 🤝 Contribuir

1. Haz **fork** y crea tu rama: `feature/lo-que-sea`.
2. Sigue las **convecciones** y el **checklist de seguridad**.
3. **Pull Request** descriptivo con capturas/diagramas en `docs/`/`img/`.

> Cualquier contribución debe evitar publicar secretos y respetar el **principio de mínima exposición**.

---

## 📄 Licencia

Este repositorio se distribuye bajo licencia **MIT**. Ver [LICENSE](./LICENSE).
