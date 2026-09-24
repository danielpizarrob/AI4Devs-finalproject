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

### **2.1. Diagrama de arquitectura:**
> Usa el formato que consideres más adecuado para representar los componentes principales de la aplicación y las tecnologías utilizadas. Explica si sigue algún patrón predefinido, justifica por qué se ha elegido esta arquitectura, y destaca los beneficios principales que aportan al proyecto y justifican su uso, así como sacrificios o déficits que implica.


### **2.2. Descripción de componentes principales:**

> Describe los componentes más importantes, incluyendo la tecnología utilizada

### **2.3. Descripción de alto nivel del proyecto y estructura de ficheros**

> Representa la estructura del proyecto y explica brevemente el propósito de las carpetas principales, así como si obedece a algún patrón o arquitectura específica.

### **2.4. Infraestructura y despliegue**

> Detalla la infraestructura del proyecto, incluyendo un diagrama en el formato que creas conveniente, y explica el proceso de despliegue que se sigue

### **2.5. Seguridad**

> Enumera y describe las prácticas de seguridad principales que se han implementado en el proyecto, añadiendo ejemplos si procede

### **2.6. Tests**

> Describe brevemente algunos de los tests realizados

---

## 3. Modelo de Datos

### **3.1. Diagrama del modelo de datos:**

> Recomendamos usar mermaid para el modelo de datos, y utilizar todos los parámetros que permite la sintaxis para dar el máximo detalle, por ejemplo las claves primarias y foráneas.


### **3.2. Descripción de entidades principales:**

> Recuerda incluir el máximo detalle de cada entidad, como el nombre y tipo de cada atributo, descripción breve si procede, claves primarias y foráneas, relaciones y tipo de relación, restricciones (unique, not null…), etc.

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

