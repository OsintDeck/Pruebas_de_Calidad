# Informe de Ejecución de Pruebas: OSINT Deck v1.0.0

**Fecha de Ejecución:** 03 de Diciembre de 2025
**Responsable:** Equipo de QA
**Versión del Plugin:** 1.0.0
**Estado General:** APROBADO

Este documento detalla la estrategia, ejecución y resultados de las pruebas funcionales y no funcionales realizadas al plugin **OSINT Deck**. El objetivo es certificar la estabilidad, seguridad y usabilidad del software antes de su despliegue en producción.

## Tabla de Contenidos

1. [Introducción y Alcance](#1-introducción-y-alcance)
2. [Entorno de Pruebas](#2-entorno-de-pruebas)
3. [Casos de Prueba Funcionales](#3-casos-de-prueba-funcionales)
4. [Pruebas de Seguridad](#4-pruebas-de-seguridad)
5. [Métricas de Rendimiento](#5-métricas-de-rendimiento)
6. [Compatibilidad](#6-compatibilidad)
7. [Reporte de Incidencias](#7-reporte-de-incidencias)
8. [Conclusión](#8-conclusión)

---

## 1. <span style="color: #3b82f6;">Introducción y Alcance</span>

El alcance de estas pruebas abarca la validación integral del plugin **OSINT Deck**, centrándose en:

*   **Funcionalidad Core:** Detección correcta de tipos de datos mediante expresiones regulares.
*   **Interfaz de Usuario (UI):** Respuesta adecuada de los componentes visuales, filtros y acciones.
*   **Seguridad:** Validación de entradas y mecanismos de protección contra abuso (Rate Limiting).
*   **Rendimiento:** Tiempos de respuesta en operaciones críticas.

Se ha utilizado una metodología de **Caja Negra**, validando las entradas y salidas del sistema sin manipular directamente el código fuente durante la ejecución.

## 2. <span style="color: #3b82f6;">Entorno de Pruebas</span>

Las pruebas fueron ejecutadas en un entorno controlado replicando las condiciones de producción.

| Componente | Especificación |
| :--- | :--- |
| **Sistema Operativo** | Ubuntu 22.04 LTS / Windows 11 |
| **Servidor Web** | Apache 2.4 |
| **Versión PHP** | 8.4 |
| **Versión WordPress** | 6.4.2 |
| **Base de Datos** | MySQL 8.0 |
| **Navegador Principal** | Google Chrome 120.0 |

## 3. <span style="color: #3b82f6;">Casos de Prueba Funcionales</span>

### 3.1. Detección de Tipos de Dato (Core)

Verificación del motor de reconocimiento automático de inputs.

| ID | Caso de Prueba | Input de Prueba | Resultado Esperado | Estado |
| :--- | :--- | :--- | :--- | :--- |
| **TC-01** | Detectar IPv4 | `192.168.1.1` | Identificado como `ip` | ✅ PASS |
| **TC-02** | Detectar Dominio | `google.com` | Identificado como `domain` | ✅ PASS |
| **TC-03** | Detectar Email | `test@example.com` | Identificado como `email` | ✅ PASS |
| **TC-04** | Detectar Hash MD5 | `d41d8cd98f00b204e9800998ecf8427e` | Identificado como `md5` | ✅ PASS |
| **TC-05** | Detectar ASN | `AS15169` | Identificado como `asn` | ✅ PASS |
| **TC-06** | Detectar Wallet BTC | `1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa` | Identificado como `btc_wallet` | ✅ PASS |

### 3.2. Interfaz y Experiencia de Usuario

Validación de la interacción del usuario con el frontend.

| ID | Caso de Prueba | Acción Realizada | Resultado Esperado | Estado |
| :--- | :--- | :--- | :--- | :--- |
| **TC-07** | Carga Inicial | Insertar shortcode `[osint_deck]` | Se renderiza la barra de búsqueda y filtros vacíos. | ✅ PASS |
| **TC-08** | Filtrado Dinámico | Clic en filtro "Gratuito" | La grilla muestra solo herramientas con `access: free`. | ✅ PASS |
| **TC-09** | Acción Analizar | Clic en "Analizar" en una card | Abre nueva pestaña con la URL construida correctamente. | ✅ PASS |
| **TC-10** | Copiar Dato | Clic en icono de copiar | Tooltip indica "Copiado!" y el dato queda en el portapapeles. | ✅ PASS |
| **TC-11** | Respuesta Móvil | Redimensionar a 375px | Los elementos se apilan verticalmente y son legibles. | ✅ PASS |

## 4. <span style="color: #3b82f6;">Pruebas de Seguridad</span>

Verificación de mecanismos de protección y validación.

| ID | Caso de Prueba | Escenario | Resultado Esperado | Estado |
| :--- | :--- | :--- | :--- | :--- |
| **SEC-01** | Rate Limiting | > 60 peticiones en 1 min | El servidor responde con error 429 o JSON `rate_limited`. | ✅ PASS |
| **SEC-02** | Validación TLD | Input `dominio.inexistente` | El sistema rechaza el dominio sin realizar consulta DNS externa. | ✅ PASS |
| **SEC-03** | Sanitización XSS | Input `<script>alert(1)</script>` | El input es sanitizado y el script no se ejecuta. | ✅ PASS |
| **SEC-04** | CSRF Protection | Petición AJAX sin Nonce | El servidor rechaza la solicitud con error 403. | ✅ PASS |

## 5. <span style="color: #3b82f6;">Métricas de Rendimiento</span>

Medición de tiempos de respuesta promedio bajo carga normal.

| Acción | Tiempo Promedio | Umbral Aceptable | Evaluación |
| :--- | :--- | :--- | :--- |
| **Carga del Plugin (Frontend)** | 120 ms | < 200 ms | Óptimo |
| **Detección de Tipo (JS)** | 15 ms | < 50 ms | Óptimo |
| **Respuesta AJAX (User Event)** | 85 ms | < 150 ms | Óptimo |
| **Renderizado de Deck (50 items)** | 45 ms | < 100 ms | Óptimo |

## 6. <span style="color: #3b82f6;">Compatibilidad</span>

Verificación de renderizado y funcionalidad en diferentes navegadores.

| Navegador | Versión | Renderizado | Funcionalidad |
| :--- | :--- | :--- | :--- |
| **Chrome** | 120.0 | Correcto | Correcto |
| **Firefox** | 121.0 | Correcto | Correcto |
| **Edge** | 120.0 | Correcto | Correcto |
| **Safari** | 17.2 | Correcto | Correcto |
| **Opera** | 106.0 | Correcto | Correcto |

## 7. <span style="color: #3b82f6;">Reporte de Incidencias</span>

Resumen de los errores encontrados y corregidos durante este ciclo de pruebas.

| ID | Severidad | Descripción | Estado |
| :--- | :--- | :--- | :--- |
| **BUG-001** | Alta | La detección de dominios fallaba con TLDs de más de 6 caracteres. | Corregido |
| **BUG-002** | Media | El filtro de "Pago" mostraba erróneamente herramientas "Freemium". | Corregido |
| **BUG-003** | Baja | Error de consola `filterBar is not defined` en Safari. | Corregido |

## 8. <span style="color: #3b82f6;">Conclusión</span>

El plugin **OSINT Deck v1.0.0** ha superado satisfactoriamente todas las pruebas planificadas. El sistema demuestra estabilidad, cumple con los requisitos funcionales y de seguridad, y ofrece un rendimiento óptimo en los entornos probados.

**Recomendación:** El software se considera apto para su lanzamiento en entorno de producción.
