# Ataques al servicio DHCP — Repositorio didáctico

**Este repositorio ofrece documentación y réplicas prácticas sobre ataques dirigidos al servicio DHCP**, sus casos conocidos, debilidades del protocolo y demostraciones para facilitar su comprensión en un entorno controlado.

> ⚠️ **Importante (uso responsable)**
>
> Las pruebas y herramientas contenidas en este repositorio están pensadas **solo** para fines educativos y de auditoría en laboratorios controlados o con permiso explícito. Ejecutar ataques en redes de terceros sin autorización es ilegal y éticamente reprobable.

---

## Contenido del repositorio

* `README.md` — Este documento.
* `docs/DHCP-Starvation/` — Réplica y material relacionado con **DHCP Starvation**.
* `docs/DHCP-Spoofing/` — Réplica y material relacionado con **DHCP Spoofing**.
* `docs/` — Base de la documentación.

---

## Índice

1. [¿Qué es DHCP?](#qué-es-dhcp)
2. [Descripción del repositorio](#descripción)
3. [Ataques incluidos](#ataques-incluidos)
4. [Objetivos](#objetivos)
5. [Requisitos y entorno de pruebas](#requisitos-y-entorno-de-pruebas)
6. [Uso recomendado (pasos rápidos)](#uso-recomendado-pasos-rápidos)
7. [Mitigaciones y buenas prácticas](#mitigaciones-y-buenas-prácticas)
8. [Contribuir](#contribuir)
9. [Licencia](#licencia)

---

## ¿Qué es DHCP?

DHCP (Dynamic Host Configuration Protocol) es un protocolo de red que automatiza la asignación de parámetros IP (dirección IP, máscara de subred, puerta de enlace, DNS, etc.) a los dispositivos que se conectan a una red. Para una explicación más detallada, ver la sección: [docs/DHCP.md](docs/DHCP.md).

---

## Descripción

Este repositorio documenta y demuestra dos ataques representativos contra el servicio DHCP:

* **DHCP Starvation**: Consiste en agotar el pool de direcciones asignables de un servidor DHCP mediante solicitudes maliciosas (por ejemplo generando múltiples solicitudes con direcciones MAC falsas), de modo que clientes legítimos no puedan obtener concesiones.

* **DHCP Spoofing**: Un atacante se hace pasar por servidor DHCP, respondiendo primero a los clientes con parámetros de red maliciosos (puertas de enlace o DNS controlados) para interceptar o manipular tráfico.

Cada carpeta de réplica contiene: objetivos, pasos para reproducir en laboratorio, scripts/herramientas usadas (cuando procede), resultados esperados y análisis.

---

## Ataques incluidos

* `docs/DHCP-Starvation/README.md` — explicación teórica, precondiciones, y pasos para ejecutar la réplica.
* `docs/DHCP-Spoofing/README.md` — explicación teórica, ejemplos y pruebas de verificación.

---

## Objetivos

1. Documentar las vulnerabilidades del protocolo DHCP que permiten ataques como *starvation* y *spoofing*.
2. Proporcionar ejemplos reproducibles y guías paso a paso para fines educativos y de auditoría.
3. Sugerir contramedidas y buenas prácticas para mitigar estos ataques en entornos reales.

---

## Requisitos y entorno de pruebas

Recomendado para el laboratorio:

* Red aislada (Se sugiere red NAT virtual) o emulación (p. ej. GNS3, EVE-NG, máquinas virtuales).
* Máquina atacante: Linux (Kali/Parrot/Ubuntu) con privilegios para enviar/recibir paquetes raw.
* Máquina víctima: cliente DHCP estándar (Windows, Linux, etc.).
* Servidor DHCP legítimo para comparar comportamientos.
* Herramientas sugeridas: `yersinia`, `dhclient`, `ettercap`, utilidades propias incluidas en las carpetas de réplica.

**Nota:** Asegúrate de tener permisos y de trabajar en un entorno de pruebas controlado.

---

## Uso recomendado (pasos rápidos)

1. **Leer** la sección `docs/DHCP.md` o `DHCP#¿Qué es el DHCP?` para entender el flujo básico del protocolo.
2. **Configurar** una red de laboratorio aislada (vm) con un servidor [DHCP legítimo](docs/ConfigurandoServidorDHCP.md).
3. **Revisar** los `README` dentro de `docs/DHCP-Starvation/` y `docs/DHCP-Spoofing/`.
4. **Seguir** las instrucciones paso a paso en cada carpeta para ejecutar las réplicas. Cada réplica contiene:

   * Precondiciones y advertencias.
   * Comandos y scripts (revisar y entender antes de ejecutar).
   * Resultados esperados y cómo comprobarlos.
5. **Analizar** los resultados y leer la sección de mitigaciones.

---

## Mitigaciones y buenas prácticas

Para reducir el riesgo de ataques contra DHCP se recomiendan entre otras las siguientes medidas:

* **Filtrado y control de acceso por puerto** (por ejemplo, 802.1X) para evitar que dispositivos no autorizados generen tráfico DHCP malicioso.
* **DHCP Snooping** en switches gestionados: permite marcar puertos de confianza y filtrar respuestas DHCP no deseadas.
* **Dynamic ARP Inspection (DAI)** junto con DHCP Snooping para bloquear ARP spoofing asociado.
* **Limitación de concesiones por MAC** y comprobaciones en el servidor DHCP si está disponible.
* **Monitoreo y alertas** sobre ráfagas inusuales de solicitudes DHCP.
* **Segmentación de red** y redes de invitados separadas.
* **Firmas y reglas IDS/IPS** para detectar patrones de starvation o spoofing.

Cada carpeta de réplica incluye una sección con recomendaciones prácticas específicas aplicadas al caso.

---

## Licencia

Este proyecto se distribuye bajo la licencia **MIT** (o la que prefieras especificar). Consulta el archivo [LICENSE](license.md) para más detalles.

---

## Referencias y lecturas recomendadas

* Documentación oficial del protocolo DHCP (RFC 2131 / RFC 2132).
* Guías de configuración de DHCP Snooping y DAI para tus switches (documentación del fabricante).
* Artículos y whitepapers sobre ataques DHCP y mitigaciones.

---
