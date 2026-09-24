## Índice

0. [Ficha del proyecto](#0-ficha-del-proyecto)
1. [Descripción general del producto](#1-descripción-general-del-producto)
2. [Arquitectura del sistema](#2-arquitectura-del-sistema)
3. [Modelo de datos](#3-modelo-de-datos)
4. [Especificación de la API](#4-especificación-de-la-api)
5. [Historias de usuario](#5-historias-de-usuario)
6. [Tickets de trabajo](#6-tickets-de-trabajo)
7. [Pull requests](#7-pull-requests)

---

## 0. Ficha del proyecto

### **0.1. Tu nombre completo:**
Daniel Pizarro Bustamante

### **0.2. Nombre del proyecto:**
Copiloto VoiceAssist para Wildix

### **0.3. Descripción breve del proyecto:**
Copiloto inteligente en tiempo real para agentes de call center basado en una extensión de navegador. Captura el audio estéreo de llamadas entrantes en webphones (como Wildix Collaboration), transcribe la conversación en tiempo real e interactúa con el CRM y la base de conocimiento para sugerir respuestas inmediatas, identificar al cliente, alertar sobre el tono emocional y automatizar el registro post-llamada sin alterar la infraestructura PBX.

### **0.4. URL del proyecto:**

> Puede ser pública o privada, en cuyo caso deberás compartir los accesos de manera segura. Puedes enviarlos a [alvaro@lidr.co](mailto:alvaro@lidr.co) usando algún servicio como [onetimesecret](https://onetimesecret.com/).

### 0.5. URL o archivo comprimido del repositorio

> Puedes tenerlo alojado en público o en privado, en cuyo caso deberás compartir los accesos de manera segura. Puedes enviarlos a [alvaro@lidr.co](mailto:alvaro@lidr.co) usando algún servicio como [onetimesecret](https://onetimesecret.com/). También puedes compartir por correo un archivo zip con el contenido


---

## 1. Descripción general del producto

> Describe en detalle los siguientes aspectos del producto:

### **1.1. Objetivo:**

Propósito y valor: Reducir los tiempos de atención (AHT), eliminar la sobrecarga cognitiva del operador y elevar la resolución en primer contacto (FCR) en centros de soporte y ventas.

Qué soluciona: Resuelve la fragmentación operativa donde el agente debe escuchar al cliente, navegar por múltiples pestañas del CRM, buscar manuales de procedimientos y redactar notas manuales al colgar.

Para quién: Diseñado para agentes de atención al cliente y ventas que operan con softphones web (específicamente Wildix Collaboration o soluciones WebRTC en navegador), así como para supervisores y áreas de calidad y compliance.

### **1.2. Características y funcionalidades principales:**

1. **Captura y Streaming de Audio Estéreo (Client-Side):**
* Captura en simultáneo el canal del cliente (`chrome.tabCapture`) y el micrófono del agente (`getUserMedia`) sin mezclarlos en mono.
* Envía ambos flujos de audio en crudo a través de un WebSocket continuo hacia el motor de procesamiento.


2. **Transcripción en Streaming de Ultra Baja Latencia:**
* Conversión continua de voz a texto (STT) con discriminación exacta de hablante (*speaker separation* nativa por canal de audio).
* Detección de pausas (*endpointing*) para segmentar turnos conversacionales en menos de 500 ms.


3. **Identificación Automática del Cliente (Zero-Click CRM Lookup):**
* Extracción de entidades clave (DNI, RUC, número telefónico o código de cliente) en los primeros segundos de la llamada.
* Consulta directa al API del CRM para desplegar una tarjeta con el perfil, antigüedad, últimas compras y tickets abiertos del llamante.


4. **Recomendación Contextual y Base de Conocimiento (RAG):**
* Detección de la consulta o reclamo del cliente y búsqueda instantánea en las políticas y manuales de la empresa.
* Entrega de respuestas concisas (en 2 o 3 viñetas procesables) y sugerencias paso a paso.


5. **Disparador Asistido / Botón de Acción Rápida (Trigger Híbrido):**
* Atajo en pantalla o teclado (`Espacio` o botón flotante) para que el operador solicite activamente la respuesta de la IA cuando el cliente plantea una consulta compleja o ambigua.


6. **Semáforo Emocional y Tips de Empatía:**
* Detección de sentimiento (Calmado, Dudoso, Frustrado, Hostil) para guiar al agente con consejos rápidos de desescalamiento.


7. **Resumen y Tipificación Post-Llamada (Auto ACW):**
* Al detectar el colgado de la llamada, la IA genera un resumen estructurado (Motivo, Problema, Solución acordada, Siguiente acción) y actualiza automáticamente el ticket en el CRM.

---
### **1.3. Diseño y experiencia de usuario:**

* **Punto de contacto y activación:**
El sistema se integra como una barra lateral (*side-panel*) no intrusiva sobre la pestaña de Wildix Collaboration / CRM. No requiere pantallas adicionales ni cambios de ventana.
* **Flujo del usuario paso a paso:**
1. **Inicio de llamada:** El agente atiende en su softphone web. La extensión detecta el canal activo y abre el panel lateral automáticamente.
2. **Identificación (0–15 s):** El cliente menciona su documento/identificador; el panel muestra la tarjeta de datos del cliente (nombre, segmento, estado de cuenta).
3. **Durante la llamada:** A medida que surgen dudas técnicas o comerciales, aparecen tarjetas de recomendación con respuestas directas y botones de "Copiar respuesta" o "Marcar resuelto".
4. **Fin de llamada:** Al colgar, aparece una tarjeta con el borrador del resumen de la llamada y un botón para confirmar el guardado en el CRM con un solo clic.


### **1.4. Instrucciones de instalación:**
> Documenta de manera precisa las instrucciones para instalar y poner en marcha el proyecto en local (librerías, backend, frontend, servidor, base de datos, migraciones y semillas de datos, etc.)

---

## 2. Arquitectura del Sistema

### 2.1. Diagrama de arquitectura

```
  ┌────────────────────────────────────────────────────────────────────────┐
  │                 CLIENTE / NAVEGADOR (PUESTO DE AGENTE)                 │
  │                                                                        │
  │   ┌───────────────────────────┐         ┌──────────────────────────┐   │
  │   │  Wildix Collaboration Tab │         │  Side Panel UI (React)   │   │
  │   │  (WebRTC Audio In/Out)    │         │  - Ficha CRM             │   │
  │   └─────────────┬─────────────┘         │  - Tarjetas de Sugerencia│   │
  │                 │                       │  - Alerta Emocional      │   │
  │                 ▼                       └────────────▲─────────────┘   │
  │   ┌───────────────────────────┐                      │                 │
  │   │ Background / Offscreen    │                      │ Push Eventos    │
  │   │ - tabCapture (Cliente)    │                      │ (UI State)      │
  │   │ - getUserMedia (Agente)   │                      │                 │
  │   │ - Web Audio API (Estéreo) │                      │                 │
  │   └─────────────┬─────────────┘                      │                 │
  └─────────────────┼────────────────────────────────────┼─────────────────┘
                    │                                    │
                    │ PCM Audio Stream (Dual-Channel)    │ WebSocket (JSON)
                    ▼                                    │
  ┌──────────────────────────────────────────────────────┴─────────────────┐
  │                  BACKEND ORCHESTRATOR (NODE.JS / FASTIFY)              │
  │                                                                        │
  │  ┌─────────────────────────┐               ┌────────────────────────┐  │
  │  │ WebSocket Gateway       │               │ Session & State Manager│  │
  │  │ (Auth, Heartbeat, VAD)  │──────────────►│ (Sliding Context & ID) │  │
  │  └───────────┬─────────────┘               └───────────┬────────────┘  │
  │              │ Audio chunks                            │               │
  │              ▼                                         │ Transcripción │
  │  ┌─────────────────────────┐                           ▼               │
  │  │ STT Streaming Client    │               ┌────────────────────────┐  │
  │  │ (Deepgram Nova-2 /      │──────────────►│ Copilot Engine / LLM   │  │
  │  │  Whisper Streaming)     │ Transcripción │ (NER, Router, Fast LLM)│  │
  │  └─────────────────────────┘               └─────┬────────────┬─────┘  │
  │                                                  │            │        │
  └──────────────────────────────────────────────────┼────────────┼────────┘
                                                     │            │
                           REST API / OAuth2         │            │ Semántico
                                                     ▼            ▼
                                             ┌───────────┐ ┌─────────────┐
                                             │  CRM API  │ │ Vector DB   │
                                             │ (Tickets, │ │ (Qdrant /   │
                                             │  Contact) │ │  Chroma /   │
                                             │           │ │  Manuales)  │
                                             └───────────┘ └─────────────┘

```

#### Patrón Arquitectónico

* **Arquitectura Orientada a Eventos (EDA) + Pipeline Asíncrono de Streaming**: Basado en eventos de audio y mensajes bidireccionales vía WebSockets.
* **Stateful Session per Call**: Cada llamada activa instancia una sesión temporal en memoria que mantiene los últimos *N* turnos de conversación (*sliding window*) y los datos de contexto del cliente recuperados del CRM.

#### Justificación de la Elección

1. **Latencia Sub-Segundo:** Los protocolos HTTP convencionales basados en sondeo (*polling*) o llamadas sincrónicas cliente-servidor degradan la experiencia. WebSockets full-duplex permiten recibir audio continuo y retornar tarjetas de sugerencia en menos de 1.2 segundos tras el fin de turno.
2. **Agnóstico de Telefonía PBX:** Al apoyarse en la captura del navegador (Chrome Extension), no se requiere alterar la infraestructura de PBX/WMS ni exponer puertos SIP/RTP externos.

#### Beneficios Principales

* **Despliegue Cero Fricción en PBX:** Funciona inmediatamente sobre cualquier agente que utilice el cliente web de Wildix.
* **Separación de Hablantes Perfecta:** Al procesar canales independientes (Canal L = Cliente, Canal R = Agente), la transcripción tiene 100% de precisión de hablante sin sobrecoste de diarización de audio.
* **Ahorro de Cómputo:** Las consultas pesadas de RAG y análisis de intención solo se disparan tras detección de pausas (*endpointing*) o mediante disparador asistido del operador.

#### Sacrificios y Déficits (Trade-offs)

* **Consumo de Recursos en Cliente:** Procesar dos flujos de audio con Web Audio API en el navegador del operador añade un ligero consumo de CPU/RAM en la máquina local.
* **Dependencia del Navegador:** Requiere el uso de Google Chrome / Chromium con la extensión corporativa instalada y permisos activos de captura de pestañas y micrófono.

---

### 2.2. Descripción de componentes principales

| Componente | Tecnología | Propósito |
| --- | --- | --- |
| **Capture & Frontend Widget** | Manifest V3, Web Audio API, React 18, TailwindCSS | Extensión de Chrome. Captura y multiplexa el audio estéreo (tab + micro), mantiene la conexión WSS y renderiza las tarjetas de sugerencia y sentimiento en el Side Panel. |
| **Media Gateway & Orchestrator** | Node.js (v20+ LTS) con Fastify y `@fastify/websocket` | Servidor backend asíncrono. Gestiona el ciclo de vida de la conexión WSS, búferes de audio en memoria y la cola de eventos por llamada. |
| **Speech-to-Text (STT)** | Deepgram Nova-2 API (o Whisper Live streaming) | Transcripción de audio a texto continua con latencia < 350 ms, soporte multi-canal nativo y soporte en español con terminología local. |
| **Inference Engine (LLM)** | Google Gemini 1.5 Flash / Claude 3.5 Haiku | Modelo liviano y de baja latencia encargado de: 1) Clasificación de sentimiento, 2) Extracción de identificadores (DNI/Teléfono), 3) Generación de respuestas guiadas y 4) Resumen post-llamada. |
| **Retrieval Augmented Generation (RAG)** | Qdrant / ChromaDB + Text Embeddings | Indexación y búsqueda semántica de manuales operativos, matrices de objeciones y políticas de soporte al cliente. |
| **CRM Connector** | Cliente HTTP Axios / Fastify Integration Module | Capa de abstracción para consultar endpoints REST del CRM (búsqueda por número/documento y persistencia de tickets y tipificaciones). |

---

### 2.3. Descripción de alto nivel del proyecto y estructura de ficheros

El repositorio se organiza bajo una estructura **Monorepo** modular, separando la lógica del cliente (extensión) del orquestador backend:

```text
callsense-ai/
├── apps/
│   ├── extension/               # Extensión de Chrome (Manifest V3)
│   │   ├── public/              # Icons y manifest.json
│   │   ├── src/
│   │   │   ├── background/      # Service Worker (gestión de pestañas y lifecycle)
│   │   │   ├── offscreen/       # Audio Capture Worker (tabCapture + getUserMedia + WebAudio)
│   │   │   ├── sidepanel/       # UI del Agente (React Components, Hooks, State)
│   │   │   └── shared/          # Interfaces TS y contratos de mensajes
│   │   └── package.json
│   │
│   └── orchestrator/            # Backend Node.js / TypeScript
│       ├── src/
│       │   ├── core/            # Gestor de llamadas y sesiones en memoria
│       │   ├── gateway/         # WebSocket handlers y codecs de audio
│       │   ├── services/
│       │   │   ├── stt/         # Cliente de streaming STT (Deepgram/Whisper)
│       │   │   ├── llm/         # Prompts, parsers y llamadas a LLMs
│       │   │   ├── rag/         # Vector DB search y reranking
│       │   │   └── crm/         # Integración REST con CRM
│       │   ├── config/          # Variables de entorno y configuraciones
│       │   └── index.ts         # Punto de entrada Fastify
│       ├── Dockerfile
│       └── package.json
│
├── docs/                        # Diagramas, especificaciones OpenAPI y prompts
├── docker-compose.yml           # Despliegue local (Orchestrator + Vector DB)
└── README.md

```

#### Patrón y Justificación

* **Clean Architecture / Hexagonal (en el backend):** La capa `services` desacopla los proveedores externos (cambiar de Deepgram a Whisper, o de un CRM a otro) sin alterar la lógica de negocio del orquestador en `core`.
* **Offscreen Pattern (en la extensión):** Obligatorio según el estándar Manifest V3 de Chrome para manipular streams de `AudioContext` de manera estable sin que el Service Worker sea suspendido por inactividad.

---

### 2.4. Infraestructura y despliegue

```
                                      ┌──────────────────────────────────────┐
                                      │            CLOUD PROVIDER            │
                                      │                                      │
[Chrome Extensions]                   │   ┌───────────────┐                  │
        │                             │   │ Cloudflare /  │                  │
        │ TLS / WSS                   │   │ Traefik (WAF) │                  │
        ▼                             │   └───────┬───────┘                  │
┌───────────────┐                     │           │ WSS Reverse Proxy        │
│ Load Balancer │─────────────────────┼───────────▼                          │
└───────────────┘                     │   ┌──────────────────────────────┐   │
                                      │   │ Orchestrator Containers      │   │
                                      │   │ (Docker / Google Cloud Run / │   │
                                      │   │  AWS ECS Fargate)            │   │
                                      │   └───────┬──────────────┬───────┘   │
                                      │           │              │           │
                                      │           ▼              ▼           │
                                      │     ┌───────────┐  ┌─────────────┐   │
                                      │     │ Redis     │  │ Qdrant      │   │
                                      │     │ (Shared   │  │ (Vector DB) │   │
                                      │     │  Session) │  └─────────────┘   │
                                      │     └───────────┘                    │
                                      └──────────────────────────────────────┘

```

#### Proceso de Despliegue (CI/CD)

1. **Integración Continua (GitHub Actions):** En cada `push` o `merge` a la rama `main`, se ejecutan linters, chequeos de tipos de TypeScript y tests unitarios.
2. **Empaquetado de la Extensión:** Se compila el bundle de la extensión de Chrome (`pnpm run build`), generando un artefacto `.zip` versionado listo para despliegue manual o distribución en la Chrome Web Store interna/privada.
3. **Contenedorización y Entrega Continua:** El backend se empaqueta en una imagen Docker ligera (Alpine-based Node.js), se escanea con Trivy para detectar vulnerabilidades y se despliega automáticamente en un clúster de contenedores (Cloud Run o ECS Fargate) protegido por terminación TLS.

---

### 2.5. Seguridad

* **Cifrado en Tránsito:** Toda la transmisión de audio y mensajes de señalización se realiza obligatoriamente sobre **WSS (WebSocket Secure)** y llamadas HTTPS cifradas con TLS 1.3.
* **Aislamiento de Permisos en el Navegador:** La extensión opera bajo el principio de mínimo privilegio en su `manifest.json`, solicitando acceso de captura de audio únicamente sobre el origen específico de la pestaña del softphone (Wildix Collaboration) y no sobre la navegación global del usuario.
* **Anonimización y Enmascaramiento de PII:** Implementación de un filtro por expresiones regulares y NER previo a la invocación de LLMs públicos para enmascarar datos de alta sensibilidad como códigos CVV/CVC, claves y números de tarjetas bancarias.
* **Autenticación Basada en Tokens:** El canal WebSocket requiere un JWT (JSON Web Token) de sesión emitido para el agente autenticado, impidiendo accesos no autorizados a las sesiones de llamada y transcripción.
* **Política de Retención de Audio:** El audio procesado en streaming no se almacena en disco en el servidor intermedio; los fragmentos (*chunks*) residen exclusivamente en memoria volátil durante el tiempo de inferencia del STT y son desechados de inmediato.

---

### 2.6. Tests

* **Tests Unitarios (Backend):** Implementados con **Vitest / Jest** sobre los módulos de procesamiento de texto:
* Pruebas de extracción de entidades (validación de expresiones regulares de DNI, teléfono y código de cliente).
* Pruebas de lógica de ventana deslizante (*sliding window*) de turnos conversacionales.


* **Tests de Integración de Audio y WebSocket:**
* Simulación de clientes WebSocket enviando streams de audio PCM sintético pregrabado para verificar que el pipeline procese los mensajes, invoque el mock del STT y retorne el payload JSON esperado en menos de 1 segundo.


* **Tests de Integración con el CRM:**
* Mocks con `msw` (Mock Service Worker) para validar el comportamiento del orquestador ante respuestas exitosas, retrasos de red o errores HTTP 404/500 en las APIs del CRM.


* **Tests de Carga y Concurrencia:**
* Pruebas ejecutadas con herramientas de benchmark de WebSockets (ej. `k6` / `Artillery`) para garantizar la estabilidad del servidor ante 50+ conexiones de audio en streaming simultáneas sin fugas de memoria.
---

## 3. Modelo de Datos

Aquí tienes una versión refinada, exhaustiva y con máxima precisión técnica para la **Sección 3: Modelo de Datos**, modelada para una base de datos relacional robusta (como PostgreSQL) que respalda tanto la analítica en caliente como la auditoría post-llamada, control de calidad (QA) y retroalimentación de IA.

---

# 3. Modelo de Datos

### 3.1. Diagrama del modelo de datos

```mermaid
erDiagram
    TENANT ||--o{ AGENT : "emplea"
    TENANT ||--o{ KNOWLEDGE_DOCUMENT : "posee"
    TENANT ||--o{ CALL_SESSION : "registra"

    AGENT ||--o{ CALL_SESSION : "atiende"
    CUSTOMER ||--o{ CALL_SESSION : "participa"

    CALL_SESSION ||--o{ TRANSCRIPT_TURN : "contiene"
    CALL_SESSION ||--o{ AI_SUGGESTION : "recibe"
    CALL_SESSION ||--o| CALL_SUMMARY : "produce"

    KNOWLEDGE_DOCUMENT ||--o{ KNOWLEDGE_CHUNK : "secciona"
    KNOWLEDGE_CHUNK |o--o{ AI_SUGGESTION : "sustenta"

    TENANT {
        uuid id PK
        varchar(100) name "NOT NULL"
        varchar(50) code UK "NOT NULL"
        boolean is_active "NOT NULL DEFAULT true"
        timestamp_tz created_at "NOT NULL DEFAULT now()"
    }

    AGENT {
        uuid id PK
        uuid tenant_id FK "NOT NULL"
        varchar(150) email UK "NOT NULL"
        varchar(120) full_name "NOT NULL"
        varchar(30) pbx_extension "NOT NULL"
        varchar(20) role "NOT NULL DEFAULT 'agent'"
        boolean is_active "NOT NULL DEFAULT true"
        timestamp_tz created_at "NOT NULL DEFAULT now()"
    }

    CUSTOMER {
        uuid id PK
        varchar(100) crm_contact_id "NULLABLE"
        varchar(25) phone_number "NOT NULL"
        varchar(20) document_id "NULLABLE"
        varchar(120) full_name "NULLABLE"
        varchar(50) customer_tier "NULLABLE"
        jsonb metadata "NOT NULL DEFAULT '{}'"
        timestamp_tz updated_at "NOT NULL DEFAULT now()"
    }

    CALL_SESSION {
        uuid id PK
        uuid tenant_id FK "NOT NULL"
        uuid agent_id FK "NOT NULL"
        uuid customer_id FK "NULLABLE"
        varchar(100) wildix_call_id UK "NOT NULL"
        varchar(20) call_direction "NOT NULL DEFAULT 'inbound'"
        varchar(20) status "NOT NULL DEFAULT 'in_progress'"
        timestamp_tz started_at "NOT NULL DEFAULT now()"
        timestamp_tz ended_at "NULLABLE"
        integer duration_seconds "NULLABLE"
        varchar(15) initial_sentiment "NULLABLE"
        varchar(15) final_sentiment "NULLABLE"
    }

    TRANSCRIPT_TURN {
        uuid id PK
        uuid call_session_id FK "NOT NULL"
        integer sequence_order "NOT NULL"
        varchar(10) speaker_role "NOT NULL"
        text content "NOT NULL"
        decimal start_offset_sec "NOT NULL"
        decimal end_offset_sec "NOT NULL"
        decimal confidence_score "NOT NULL"
        timestamp_tz created_at "NOT NULL DEFAULT now()"
    }

    AI_SUGGESTION {
        uuid id PK
        uuid call_session_id FK "NOT NULL"
        uuid knowledge_chunk_id FK "NULLABLE"
        varchar(30) suggestion_type "NOT NULL"
        text suggested_text "NOT NULL"
        jsonb action_payload "NOT NULL DEFAULT '{}'"
        decimal triggered_at_sec "NOT NULL"
        varchar(20) trigger_source "NOT NULL DEFAULT 'auto'"
        varchar(20) agent_action "NOT NULL DEFAULT 'ignored'"
        integer latency_ms "NOT NULL"
        timestamp_tz created_at "NOT NULL DEFAULT now()"
    }

    CALL_SUMMARY {
        uuid id PK
        uuid call_session_id FK UK "NOT NULL"
        text main_reason "NOT NULL"
        text solution_provided "NOT NULL"
        text pending_tasks "NULLABLE"
        varchar(30) disposition_code "NOT NULL"
        boolean crm_synced "NOT NULL DEFAULT false"
        timestamp_tz synced_at "NULLABLE"
        timestamp_tz created_at "NOT NULL DEFAULT now()"
    }

    KNOWLEDGE_DOCUMENT {
        uuid id PK
        uuid tenant_id FK "NOT NULL"
        varchar(200) title "NOT NULL"
        varchar(50) category "NOT NULL"
        varchar(255) source_url "NULLABLE"
        integer version "NOT NULL DEFAULT 1"
        boolean is_active "NOT NULL DEFAULT true"
        timestamp_tz created_at "NOT NULL DEFAULT now()"
    }

    KNOWLEDGE_CHUNK {
        uuid id PK
        uuid knowledge_document_id FK "NOT NULL"
        integer chunk_index "NOT NULL"
        text chunk_text "NOT NULL"
        varchar(100) vector_point_id UK "NOT NULL"
        jsonb metadata "NOT NULL DEFAULT '{}'"
        timestamp_tz created_at "NOT NULL DEFAULT now()"
    }

```

---

### 3.2. Descripción de entidades principales

#### 1. Entidad: `TENANT` (Organización / Empresa)

Permite aislar datos en caso de uso multi-cliente o diferentes sedes operativas de un mismo call center.

* **Atributos:**
* `id` (`UUID`, PK): Identificador único global del tenant.
* `name` (`VARCHAR(100)`, NOT NULL): Razón social o nombre descriptivo.
* `code` (`VARCHAR(50)`, NOT NULL, UNIQUE): Slug identificador alfanumérico.
* `is_active` (`BOOLEAN`, NOT NULL, DEFAULT `true`): Estado de suscripción/operatividad.
* `created_at` (`TIMESTAMPTZ`, NOT NULL, DEFAULT `now()`): Fecha y hora de alta.


* **Relaciones:** 1:N con `AGENT`, `KNOWLEDGE_DOCUMENT` y `CALL_SESSION`.

---

#### 2. Entidad: `AGENT` (Agente de Call Center)

Operador humano que interactúa con la extensión de navegador en Wildix.

* **Atributos:**
* `id` (`UUID`, PK): Identificador único del agente.
* `tenant_id` (`UUID`, FK, NOT NULL): Referencia a `TENANT(id)`.
* `email` (`VARCHAR(150)`, NOT NULL, UNIQUE): Correo corporativo del operador.
* `full_name` (`VARCHAR(120)`, NOT NULL): Nombre y apellido del agente.
* `pbx_extension` (`VARCHAR(30)`, NOT NULL): Número de anexo o extensión en Wildix PBX (ej. "4010").
* `role` (`VARCHAR(20)`, NOT NULL, DEFAULT `'agent'`): Nivel de rol (`'agent'`, `'supervisor'`, `'admin'`).
* `is_active` (`BOOLEAN`, NOT NULL, DEFAULT `true`): Permiso de acceso activo al sistema.
* `created_at` (`TIMESTAMPTZ`, NOT NULL, DEFAULT `now()`).


* **Relaciones:** 1:N con `CALL_SESSION`.

---

#### 3. Entidad: `CUSTOMER` (Cliente / Llamante)

Registro del cliente consultado o enriquecido mediante el CRM a través de la llamada.

* **Atributos:**
* `id` (`UUID`, PK): Identificador interno del cliente.
* `crm_contact_id` (`VARCHAR(100)`, NULLABLE): ID foráneo en el CRM del cliente (HubSpot, Salesforce, etc.).
* `phone_number` (`VARCHAR(25)`, NOT NULL): Número telefónico normalizado en formato E.164.
* `document_id` (`VARCHAR(20)`, NULLABLE): DNI, RUC, o documento de identidad extraído de la llamada.
* `full_name` (`VARCHAR(120)`, NULLABLE): Nombre recuperado del CRM o inferido por la IA.
* `customer_tier` (`VARCHAR(50)`, NULLABLE): Clasificación del cliente (ej. `'Standard'`, `'VIP'`, `'Riesgo de Fuga'`).
* `metadata` (`JSONB`, NOT NULL, DEFAULT `'{}'`): Objeto flexible que contiene datos dinámicos devueltos por el CRM (últimos tickets, saldos, productos contratados).
* `updated_at` (`TIMESTAMPTZ`, NOT NULL, DEFAULT `now()`).


* **Relaciones:** 1:N con `CALL_SESSION`.

---

#### 4. Entidad: `CALL_SESSION` (Sesión de Llamada)

Entidad medular que conecta la llamada de telefonía en Wildix con el ciclo de vida del copiloto.

* **Atributos:**
* `id` (`UUID`, PK): Identificador único de la sesión del copiloto.
* `tenant_id` (`UUID`, FK, NOT NULL): Referencia a `TENANT(id)`.
* `agent_id` (`UUID`, FK, NOT NULL): Referencia al operador en `AGENT(id)`.
* `customer_id` (`UUID`, FK, NULLABLE): Referencia a `CUSTOMER(id)` (puede ser nulo si no se logra asociar cliente).
* `wildix_call_id` (`VARCHAR(100)`, NOT NULL, UNIQUE): Identificador nativo SIP/WebRTC (`Call-ID`) de Wildix para reconciliación.
* `call_direction` (`VARCHAR(20)`, NOT NULL, DEFAULT `'inbound'`): Dirección (`'inbound'` o `'outbound'`).
* `status` (`VARCHAR(20)`, NOT NULL, DEFAULT `'in_progress'`): Ciclo de vida (`'in_progress'`, `'completed'`, `'dropped'`, `'error'`).
* `started_at` (`TIMESTAMPTZ`, NOT NULL, DEFAULT `now()`): Marca de inicio de captura del stream.
* `ended_at` (`TIMESTAMPTZ`, NULLABLE): Marca de cierre al colgar.
* `duration_seconds` (`INTEGER`, NULLABLE): Duración total de la llamada en segundos.
* `initial_sentiment` (`VARCHAR(15)`, NULLABLE): Sentimiento detectado en los primeros 60s (`'calm'`, `'frustrated'`, `'angry'`).
* `final_sentiment` (`VARCHAR(15)`, NULLABLE): Sentimiento registrado al concluir el diálogo.


* **Relaciones:**
* 1:N con `TRANSCRIPT_TURN`.
* 1:N con `AI_SUGGESTION`.
* 1:1 con `CALL_SUMMARY`.



---

#### 5. Entidad: `TRANSCRIPT_TURN` (Turno de Diálogo)

Almacena la secuencia cronológica de intervenciones devuelta por el motor STT en streaming.

* **Atributos:**
* `id` (`UUID`, PK): Identificador único del turno.
* `call_session_id` (`UUID`, FK, NOT NULL): Referencia a `CALL_SESSION(id)`.
* `sequence_order` (`INTEGER`, NOT NULL): Número ordinal del turno en la conversación (1, 2, 3...).
* `speaker_role` (`VARCHAR(10)`, NOT NULL): Rol del hablante (`'agent'` para micrófono, `'customer'` para audio de pestaña).
* `content` (`TEXT`, NOT NULL): Transcripción final estabilizada del turno.
* `start_offset_sec` (`DECIMAL(8,3)`, NOT NULL): Segundo en que comenzó a hablar respecto al inicio de la llamada.
* `end_offset_sec` (`DECIMAL(8,3)`, NOT NULL): Segundo en que finalizó la frase.
* `confidence_score` (`DECIMAL(4,3)`, NOT NULL): Nivel de confianza retornado por el STT (0.000 a 1.000).
* `created_at` (`TIMESTAMPTZ`, NOT NULL, DEFAULT `now()`).


* **Restricciones:** Clave compuesta única opcional `(call_session_id, sequence_order)`.

---

#### 6. Entidad: `AI_SUGGESTION` (Recomendación / Tarjeta de Copiloto)

Registra cada tarjeta mostrada en la extensión del agente, evaluando latencia y efectividad.

* **Atributos:**
* `id` (`UUID`, PK): Identificador único de la sugerencia.
* `call_session_id` (`UUID`, FK, NOT NULL): Referencia a `CALL_SESSION(id)`.
* `knowledge_chunk_id` (`UUID`, FK, NULLABLE): Referencia al fragmento RAG usado como base (si aplica).
* `suggestion_type` (`VARCHAR(30)`, NOT NULL): Categoría (`'rag_answer'`, `'sentiment_alert'`, `'compliance_warning'`, `'crm_quick_action'`).
* `suggested_text` (`TEXT`, NOT NULL): Mensaje o viñetas procesables desplegadas al agente.
* `action_payload` (`JSONB`, NOT NULL, DEFAULT `'{}'`): Metadatos adicionales para la UI (enlaces directos, datos a copiar con un clic).
* `triggered_at_sec` (`DECIMAL(8,3)`, NOT NULL): Segundo de la llamada en que se envió la recomendación.
* `trigger_source` (`VARCHAR(20)`, NOT NULL, DEFAULT `'auto'`): Origen (`'auto'` si la detectó el LLM, `'manual'` si el agente presionó el botón de asistencia).
* `agent_action` (`VARCHAR(20)`, NOT NULL, DEFAULT `'ignored'`): Interacción del operador (`'copied'`, `'liked'`, `'disliked'`, `'dismissed'`, `'ignored'`).
* `latency_ms` (`INTEGER`, NOT NULL): Milisegundos transcurridos desde el fin del turno de voz hasta la entrega de la sugerencia en pantalla.
* `created_at` (`TIMESTAMPTZ`, NOT NULL, DEFAULT `now()`).



---

#### 7. Entidad: `CALL_SUMMARY` (Resumen Post-Llamada / ACW)

Resultado del procesamiento tras colgar la llamada para actualizar el CRM.

* **Atributos:**
* `id` (`UUID`, PK): Identificador único del resumen.
* `call_session_id` (`UUID`, FK, NOT NULL, UNIQUE): Relación 1:1 estricta con `CALL_SESSION(id)`.
* `main_reason` (`TEXT`, NOT NULL): Causa de contacto sintetizada en 1 o 2 oraciones.
* `solution_provided` (`TEXT`, NOT NULL): Acciones tomadas o respuesta entregada por el operador.
* `pending_tasks` (`TEXT`, NULLABLE): Compromisos adquiridos o tareas de seguimiento (*follow-ups*).
* `disposition_code` (`VARCHAR(30)`, NOT NULL): Tipificación estandarizada para el CRM (ej. `'soporte_resuelto'`, `'reclamo_escalado'`, `'venta_perdida'`).
* `crm_synced` (`BOOLEAN`, NOT NULL, DEFAULT `false`): Estado de sincronización hacia el CRM.
* `synced_at` (`TIMESTAMPTZ`, NULLABLE): Marca temporal en que la API del CRM aceptó el resumen.
* `created_at` (`TIMESTAMPTZ`, NOT NULL, DEFAULT `now()`).



---

#### 8. Entidad: `KNOWLEDGE_DOCUMENT` (Documento de Base de Conocimiento)

Metadatos del documento maestro (políticas, instructivos, guías de atención).

* **Atributos:**
* `id` (`UUID`, PK): Identificador único del documento.
* `tenant_id` (`UUID`, FK, NOT NULL): Referencia a `TENANT(id)`.
* `title` (`VARCHAR(200)`, NOT NULL): Título legible del procedimiento.
* `category` (`VARCHAR(50)`, NOT NULL): Área (ej. `'Facturación'`, `'Planes'`, `'Soporte'`).
* `source_url` (`VARCHAR(255)`, NULLABLE): Enlace al documento original en Notion, Confluence o SharePoint.
* `version` (`INTEGER`, NOT NULL, DEFAULT `1`): Control de versiones.
* `is_active` (`BOOLEAN`, NOT NULL, DEFAULT `true`): Disponibilidad para indexación semántica.
* `created_at` (`TIMESTAMPTZ`, NOT NULL, DEFAULT `now()`).


* **Relaciones:** 1:N con `KNOWLEDGE_CHUNK`.

---

#### 9. Entidad: `KNOWLEDGE_CHUNK` (Fragmento de Conocimiento / RAG)

Segmentos de texto extraídos del documento maestro e indexados vectorialmente.

* **Atributos:**
* `id` (`UUID`, PK): Identificador único del fragmento.
* `knowledge_document_id` (`UUID`, FK, NOT NULL): Referencia a `KNOWLEDGE_DOCUMENT(id)`.
* `chunk_index` (`INTEGER`, NOT NULL): Posición secuencial del fragmento dentro del documento.
* `chunk_text` (`TEXT`, NOT NULL): Texto plano procesado para el contexto del LLM.
* `vector_point_id` (`VARCHAR(100)`, NOT NULL, UNIQUE): Identificador del vector en la base de datos vectorial (Qdrant/Chroma).
* `metadata` (`JSONB`, NOT NULL, DEFAULT `'{}'`): Etiquetas semánticas para filtros híbridos.
* `created_at` (`TIMESTAMPTZ`, NOT NULL, DEFAULT `now()`).


* **Relaciones:** 1:N opcional con `AI_SUGGESTION` (Trazabilidad estricta de qué fragmento originó cada sugerencia).
---

## 4. Especificación de la API

> Si tu backend se comunica a través de API, describe los endpoints principales (máximo 3) en formato OpenAPI. Opcionalmente puedes añadir un ejemplo de petición y de respuesta para mayor claridad

---

## 5. Historias de Usuario

> Documenta 3 de las historias de usuario principales utilizadas durante el desarrollo, teniendo en cuenta las buenas prácticas de producto al respecto.

**Historia de Usuario 1**

**Historia de Usuario 2**

**Historia de Usuario 3**

---

## 6. Tickets de Trabajo

> Documenta 3 de los tickets de trabajo principales del desarrollo, uno de backend, uno de frontend, y uno de bases de datos. Da todo el detalle requerido para desarrollar la tarea de inicio a fin teniendo en cuenta las buenas prácticas al respecto. 

**Ticket 1**

**Ticket 2**

**Ticket 3**

---

## 7. Pull Requests

> Documenta 3 de las Pull Requests realizadas durante la ejecución del proyecto

**Pull Request 1**

**Pull Request 2**

**Pull Request 3**

