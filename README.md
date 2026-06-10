# Auditoría Técnica de Krayin CRM (v2.1)

## 1. Información General
- **Autor:** Hans Eliot Herzfelder
- **Tutor:** Raimundo Alcázar Quesada
- **Institución:** IME Smart Education — Máster en Ciberseguridad
- **Fecha:** Marzo de 2026
- **Objetivo:** Evaluar la robustez técnica de Krayin CRM (v2.1), solución de código abierto basada en Laravel, mediante su despliegue en entorno virtualizado, análisis sistemático de vulnerabilidades y explotación controlada de los hallazgos.

## 2. Metodología y Herramientas
El proyecto simula un encargo profesional entre la firma ficticia Umbrella Security Labs y una pequeña empresa del sector servicios. El alcance técnico se rige por los estándares OWASP Top 10 y PTES.

**Herramientas empleadas:**
- **Entorno:** Hipervisor Proxmox para replicar un escenario de producción real.
- **Reconocimiento:** Nmap (escaneo de puertos), WhatWeb y cURL (fingerprinting), ffuf (enumeración de directorios).
- **Escaneo dinámico (DAST):** OWASP ZAP, Nikto, Skipfish y Wapiti.
- **Análisis estático (SAST):** Psalm (taint analysis) y grep para revisión manual de código PHP/Laravel.
- **Explotación:** Burp Suite (interceptación y manipulación HTTP), Python (scripts personalizados) y Kali Linux.

## 3. Fases del Proyecto
- **Preparación y despliegue:** Instalación de Krayin CRM sobre Ubuntu Server con Apache, expuesto exclusivamente por HTTPS (puerto 443).
- **Reconocimiento y escaneo:** Identificación del stack tecnológico y detección de fallos de configuración, incluyendo cabeceras de seguridad ausentes y archivos expuestos (`robots.txt`, `web.config`).
- **Auditoría de código fuente:** Revisión de dependencias en busca de CVEs conocidos y trazado de flujos de datos para detectar puntos de inyección.
- **Explotación:** Validación práctica de los hallazgos mediante ataques dirigidos y reproducibles.
- **Análisis e informe:** Categorización de riesgos y evaluación de su impacto sobre la tríada CIA (Confidencialidad, Integridad, Disponibilidad).

## 4. Hallazgos Principales y Criticidad
La auditoría reveló que, si bien el backend presenta una protección sólida frente a inyecciones SQL directas, existen fallos críticos en la capa de aplicación y en la configuración del servidor:

| Vulnerabilidad | Categoría | Criticidad | Estado |
| :--- | :--- | :--- | :--- |
| XSS Persistente | Inyección | Alta | Confirmada |
| Fuerza Bruta | Autenticación | Alta | Confirmada |
| Dependencias Vulnerables | Gestión de Parches | Media | Potencial |
| Clickjacking | Configuración Web | Media | Confirmada |
| Divulgación de Información | Reconocimiento | Baja | Detectada |

**Detalle de las vulnerabilidades de mayor criticidad:**
- **XSS Persistente:** Permite inyectar etiquetas `<script>` en campos de perfil de usuario. La ausencia del flag `HttpOnly` en la cookie `XSRF-TOKEN` y la falta de una política CSP agravan el riesgo de secuestro de sesión.
- **Fuerza Bruta:** Se comprometió el acceso mediante un script Python que elude los tokens anti-CSRF, aprovechando la ausencia de mecanismos de bloqueo de cuenta tras múltiples intentos fallidos.
- **Dependencias:** Se identificaron librerías desactualizadas con vulnerabilidades conocidas: `phpspreadsheet` (CVE-2025-54370) y `symfony/http-foundation` (CVE-2025-64500).

## 5. Conclusiones y Recomendaciones
El riesgo global de la aplicación se clasifica como **ALTO**. Aunque la lógica de acceso a datos es robusta, la exposición a ataques XSS y de fuerza bruta compromete directamente la confidencialidad de la información comercial gestionada por el CRM.

**Medidas correctivas prioritarias:**
- Actualizar todas las dependencias a versiones sin vulnerabilidades conocidas.
- Implementar cabeceras de seguridad HTTP (`X-Frame-Options`, `HSTS`, `Content-Security-Policy`).
- Introducir mecanismos de protección ante fuerza bruta: límite de intentos, CAPTCHA y bloqueo temporal de cuentas.
- Aplicar sanitización estricta y escapado contextual de todos los datos introducidos por el usuario para prevenir XSS.