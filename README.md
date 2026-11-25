# Cloudflare WAF Hardening Ruleset y Blueprint de Seguridad Perimetral

[![Cloudflare WAF](https://img.shields.io/badge/Cloudflare-WAF%20Hardened-F38020.svg?logo=cloudflare)](https://cloudflare.com)
[![Diagnostics Suite](https://img.shields.io/badge/TAVARDT-Diagnostics-5b3cf5.svg)](https://tools.tavardt.com/mx/analisis-cabeceras-seguridad/)
[![Licence MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

***

Conjunto de reglas personalizadas de Cloudflare WAF, mitigación de bots y blueprint de seguridad perimetral desarrollado por **TAVARDT**, en cumplimiento con los requerimientos de la LFPDPPP y estándares internacionales ISO/IEC 27001.

> [!IMPORTANT]
> **Arquitectura Perimetral Universal y Agnóstica de Pila:** Este conjunto de reglas no se limita a WordPress ni a plataformas monolíticas. Ha sido diseñado para proteger APIs RESTful, microservicios modernos, frontends SPA (Next.js, React, Vue) y backends (Node.js, Python, Go, Laravel, Java). El descarte de peticiones dirigidas a CMS heredados opera como una capa activa de higiene perimetral, bloqueando el tráfico de botnets directamente en los centros de datos perimetrales de Cloudflare en menos de 1ms antes de consumir recursos de origen.

***

## Capacidades Principales y Arquitectura Defensiva

* **Aislamiento Perimetral de Credenciales**: Bloquea de inmediato en el perímetro el acceso a archivos `.env`, directorios `.git`, volcados `.sql`, `config.json`, `package.json` y `composer.json` antes de alcanzar el origen.
* **Supresión de Herramientas de Reconocimiento**: Intercepta analizadores automáticos de vulnerabilidades (Nikto, SQLMap, Acunetix, Masscan, Zgrab, Nmap, DirBuster, Gobuster) en el borde de Cloudflare.
* **Higiene Perimetral Agnóstica de Pila**: Neutraliza vectores de ataque dirigidos a CMS obsoletos (`/xmlrpc.php`, trackbacks) en menos de 1ms, evitando que el ruido de bots sature la CPU y los sockets del servidor de origen.
* **Saneamiento de Métodos HTTP**: Rechaza métodos peligrosos o de depuración (`TRACE`, `TRACK`, `DEBUG`) directamente en la barrera perimetral del firewall.
* **Desafío Adaptativo en Rutas Administrativas**: Aplica Desafíos Gestionados de Cloudflare en portales administrativos (`/admin`, `/wp-login.php`, `/administrator`), permitiendo el tráfico autorizado de ASN interno.

***

## Vectores de Riesgo e Impacto Operacional

Desplegar servicios web y APIs públicas sin filtrado perimetral declarativo expone los servidores de origen a constantes escaneos automatizados y agotamiento de recursos. El paquete mitiga cinco vectores principales en la frontera de la red:

* **Exposición de Credenciales y Archivos de Entorno (TAVARDT-WAF-01)**: Explotación de servidores desconfigurados para sustraer secretos en variables de entorno, historiales Git y volcados de bases de datos.
* **Escaneos Automatizados de Explotación (TAVARDT-WAF-02)**: Sondeos sistemáticos de herramientas de escaneo que consumen ancho de banda, elevan el uso de CPU y buscan fallos conocidos.
* **Amplificación y Fuerza Bruta en CMS (TAVARDT-WAF-03 y TAVARDT-WAF-05)**: Redes de bots intentando amplificar denegaciones de servicio mediante XML-RPC o ejecutando ataques de fuerza bruta en paneles de acceso.
* **Abuso de Protocolo mediante Verbos HTTP Inseguros (TAVARDT-WAF-04)**: Actores hostiles que emplean métodos obsoletos para evadir memorias caché, realizar cross-site tracing (XST) o forzar modos de depuración.
* **Agotamiento de Capacidad y Caída de Servicios de Origen**: Peticiones maliciosas no filtradas que saturan hilos de ejecución en el servidor, provocando demoras y caídas para usuarios legítimos.

***

## Despliegue y Automatización Perimetral

Aplique el conjunto de reglas defensivas mediante la API v4 declarativa de Cloudflare o a través de infraestructura como código con Terraform:

### Opción A: Ingesta Directa vía Cloudflare API v4

```bash
# Desplegar reglas de firewall personalizadas mediante Cloudflare API v4
curl -X PUT "https://api.cloudflare.com/client/v4/zones/YOUR_ZONE_ID/rulesets/phases/http_request_firewall_custom/entrypoint" \
  -H "Authorization: Bearer YOUR_CLOUDFLARE_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d @rules/ruleset.json
```

### Opción B: Aprovisionamiento Declarativo con Terraform

```bash
# Navegar al directorio del módulo de Terraform
cd terraform

# Inicializar el proveedor de Cloudflare
terraform init

# Planificar el despliegue declarativo de reglas
terraform plan -var="zone_id=YOUR_ZONE_ID"

# Aplicar la configuración de firewall en el perímetro
terraform apply -var="zone_id=YOUR_ZONE_ID"
```

***

## Preguntas Técnicas Frecuentes

### ¿Qué vectores de riesgo mitiga el conjunto de reglas WAF de Cloudflare en el borde de la red?

El conjunto bloquea analizadores automatizados de reconocimiento, intentos de extracción de archivos sensibles y variables de entorno, abuso de XML-RPC y métodos HTTP de depuración directamente en Cloudflare. Esta filtración perimetral neutraliza el tráfico hostil en menos de 1 milisegundo, resguardando la CPU, la memoria y las conexiones de base de datos del servidor de origen.

### ¿Por qué se recomienda este modelo de blindaje para APIs REST y microservicios modernos además de CMS?

Los escáneres automáticos de vulnerabilidades rastrean rutas públicas de forma indiscriminada sin importar si la aplicación corre sobre Node.js, Go, Python o Next.js. Descartar sondeos ciegos de CMS y peticiones a archivos confidenciales en el perímetro garantiza una higiene perimetral limpia sin sobrecarga operativa.

### ¿Cómo previene el conjunto de reglas la fuga accidental de credenciales y volcados de bases de datos?

La regla TAVARDT-WAF-01 ejecuta un bloqueo inmediato HTTP 403 para peticiones cuyas rutas incluyan `.env`, `.git`, `.sql`, `composer.json` o `package.json`. Este bloqueo perimetral determinista impide que desajustes en el servidor de origen revelen claves privadas de API y estructuras de datos confidenciales.

### ¿Cómo opera la validación de rutas administrativas sin bloquear servicios autorizados?

La regla TAVARDT-WAF-05 activa Desafíos Gestionados de Cloudflare al detectar intentos de acceso a `/wp-login.php`, `/admin` o `/administrator`. Incluye una excepción formal para el ASN 13335 de Cloudflare, permitiendo que tareas programadas y sondas legítimas de supervisión operen con normalidad.

### ¿Cuáles son las diferencias operativas entre implementar vía API v4 de Cloudflare y usar Terraform?

Importar `rules/ruleset.json` a través de la API v4 proporciona un despliegue directo y sin dependencias externas en la fase `http_request_firewall_custom`. Aprovisionar con el módulo `terraform/main.tf` permite gestionar la seguridad como código, auditar cambios y asegurar consistencia en entornos de producción multizona.

***

## Enlaces Útiles

* **Portal Corporativo:** https://ag.tavardt.com/mx/
* **Suite de Diagnósticos:** https://tools.tavardt.com/mx/
* **Contacto Técnico:** https://ag.tavardt.com/mx/contacto/

***

### Licencia
Distribuido bajo la Licencia MIT. Consulte [LICENSE](LICENSE) para obtener detalles completos.

***

© 2026 TAVARDT. Todos los derechos reservados.
