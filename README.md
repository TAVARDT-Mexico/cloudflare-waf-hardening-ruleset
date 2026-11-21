# Cloudflare WAF Hardening Ruleset y Guía de Seguridad Perimetral | TAVARDT México

[![Licence MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Cloudflare WAF](https://img.shields.io/badge/Cloudflare-WAF%20Hardened-F38020.svg?logo=cloudflare)](https://cloudflare.com)
[![Language](https://img.shields.io/badge/Language-Español%20(México)-green.svg)](README.md)
[![Diagnostics Suite](https://img.shields.io/badge/TAVARDT-Diagnostics-5b3cf5.svg)](https://tools.tavardt.com/mx/analisis-cabeceras-seguridad/)

Reglas avanzadas de WAF y mitigación perimetral para Cloudflare, alineadas con las medidas de seguridad exigidas por la LFPDPPP y recomendaciones del INAI.

Desarrollado y mantenido por el equipo de ingeniería perimetral de **[TAVARDT México](https://ag.tavardt.com/mx/ciberseguridad/)**.

---

## 1. Arquitectura de Seguridad Perimetral

Este repositorio contiene reglas declarativas para Cloudflare concebidas para blindar la infraestructura digital de organizaciones en México contra escaneos maliciosos y fuga de credenciales:

* **Bloqueo Perimetral de Archivos Confidenciales:** Contención en el borde de peticiones dirigidas a variables `.env`, repositorios `.git`, respaldos de base de datos `.sql` y archivos de dependencias (`composer.json`, `package.json`).
* **Mitigación Automatizada de Escáneres de Vulnerabilidades:** Intercepción inmediata de herramientas de reconocimiento intrusivo (Nikto, SQLMap, Acunetix, Masscan, Nmap, Gobuster, DirBuster).
* **Protección Perimetral de Plataformas CMS:** Bloqueo de peticiones abusivas hacia `/xmlrpc.php` y `/wp-trackback.php`.
* **Restricción de Verbos HTTP de Depuración:** Rechazo en la frontera de red de métodos innecesarios (`TRACE`, `TRACK`, `DEBUG`).
* **Desafío Administrado para Rutas Críticas:** Validación automatizada ante intentos de acceso a consolas administrativas (`/wp-login.php`, `/admin`).

---

## 2. Diagnóstico Previo de Seguridad

Antes de aplicar reglas en producción, analice las cabeceras HTTP y la postura de seguridad de su dominio con la plataforma de diagnóstico de TAVARDT:

* **Auditoría de Cabeceras y Certificados SSL:** [Análisis de Cabeceras de Seguridad TAVARDT](https://tools.tavardt.com/mx/analisis-cabeceras-seguridad/)
* **Suite Completa de Diagnósticos:** [TAVARDT Tools México](https://tools.tavardt.com/mx/)

---

## 3. Implementación Rápida

### Opción A: Despliegue con Cloudflare API v4

Implemente el archivo estructurado [`rules/ruleset.json`](rules/ruleset.json):

```bash
curl -X PUT "https://api.cloudflare.com/client/v4/zones/SU_ZONE_ID/rulesets/phases/http_request_firewall_custom/entrypoint" \
     -H "Authorization: Bearer SU_CLOUDFLARE_API_TOKEN" \
     -H "Content-Type: application/json" \
     --data @rules/ruleset.json
```

### Opción B: Aprovisionamiento con Terraform

Utilice la configuración declarativa incluida en [`terraform/main.tf`](terraform/main.tf):

```bash
cd terraform
terraform init
terraform plan -var="zone_id=SU_ZONE_ID"
terraform apply -var="zone_id=SU_ZONE_ID"
```

---

## 4. Consultoría Especializada y Asesoría Perimetral

Si su organización requiere diagnósticos a profundidad, blindaje de agentes de IA o cumplimiento de normativas de protección de datos personales:

* **Portal Corporativo:** [TAVARDT México](https://ag.tavardt.com/mx/ciberseguridad/)
* **Contacto Técnico Directo:** [Atención Técnica Especializada](https://ag.tavardt.com/mx/contacto/)

---

## Licencia

Distribuido bajo licencia de código abierto MIT. Consulte el archivo [`LICENSE`](LICENSE) para más detalles.  
Copyright (c) 2026 TAVARDT México.
