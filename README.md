# Resumen: Auditoría Técnica de Krayin CRM (v2.1)

## 1. Información General
- **Autor:** Hans Eliot Herzfelder.
- **Tutor:** Raimundo Alcázar Quesada.
- **Institución:** IME Smart Education - Máster en Ciberseguridad.
- **Fecha:** Marzo de 2026.
- **Objetivo Principal:** Evaluar la robustez técnica de Krayin CRM (v2.1), una solución de código abierto basada en Laravel, mediante despliegue en entorno virtualizado, análisis de vulnerabilidades y explotación controlada.

## 2. Metodología y Herramientas
El proyecto simula una colaboración profesional entre la firma ficticia Umbrella Security Labs y una pequeña empresa del sector servicios. Se utilizaron estándares como OWASP Top 10 y PTES.

**Herramientas clave:**
- **Entorno:** Hipervisor Proxmox para replicar un escenario de producción real.
- **Reconocimiento:** Nmap (puertos), WhatWeb y Curl (fingerprinting), Ffuf (mapeo de directorios).
- **Escaneo Dinámico (DAST):** OWASP ZAP, Nikto, Skipfish y Wapiti.
- **Análisis Estático (SAST):** Psalm (taint-analysis) y comandos Grep para revisión manual de código PHP/Laravel.
- **Explotación:** Burp Suite (manipulación HTTP), Python (scripts de fuerza bruta) y Kali Linux.

## 3. Fases del Proyecto
- **Preparación y Despliegue:** Instalación de Krayin CRM sobre Ubuntu con Apache, configurado exclusivamente por HTTPS (puerto 443).
- **Reconocimiento y Escaneo:** Identificación de tecnologías y detección de fallos de configuración (como falta de cabeceras de seguridad o archivos expuestos como robots.txt y web.config).
- **Auditoría de Código Fuente:** Revisión de dependencias para hallar CVEs conocidos y análisis de flujos de datos.
- **Explotación:** Validación práctica de los hallazgos mediante ataques dirigidos.
- **Análisis e Informe:** Categorización de riesgos e impacto en la tríada de seguridad (CIA).

## 4. Hallazgos Principales y Criticidad
La auditoría reveló que, aunque el backend es robusto contra inyecciones SQL directas, existen fallos críticos en la capa de aplicación y configuración:

| Vulnerabilidad | Categoría | Criticidad | Estado |
| :--- | :--- | :--- | :--- |
| XSS Persistente | Inyección | Alta | Confirmada |
| Fuerza Bruta exitosa | Autenticación | Alta | Confirmada |
| Dependencias Vulnerables | Gestión de Parches | Media | Potencial |
| Clickjacking | Configuración Web | Media | Confirmada |
| Divulgación de Información | Reconocimiento | Baja | Detectada |

**Detalles de vulnerabilidades críticas:**
- **XSS Persistente:** Permite inyectar etiquetas `<script>` en campos de perfil. La falta de bandera `httponly` en la cookie `XSRF-TOKEN` y la ausencia de una política CSP agravan el riesgo de secuestro de sesión.
- **Fuerza Bruta:** Se logró comprometer el acceso mediante un script personalizado en Python que evade los tokens anti-CSRF, debido a la falta de mecanismos de bloqueo de cuenta.
- **Dependencias:** Se detectaron librerías obsoletas con vulnerabilidades conocidas: `phpspreadsheet` (CVE-2025-54370) y `symfony/http-foundation` (CVE-2025-64500).

## 5. Conclusiones y Recomendaciones
El riesgo global se sitúa en un nivel **ALTO**. Aunque la base del código es sólida en protección de datos, la facilidad para realizar ataques de XSS y fuerza bruta compromete la confidencialidad de la información comercial.

**Se recomienda:**
- Actualizar dependencias a versiones seguras.
- Implementar cabeceras de seguridad (X-Frame-Options, HSTS, CSP).
- Añadir capas de protección contra fuerza bruta (bloqueo de intentos).
- Sanear rigurosamente los datos introducidos por usuarios para evitar XSS.