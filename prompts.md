> Detalla en esta sección los prompts principales utilizados durante la creación del proyecto, que justifiquen el uso de asistentes de código en todas las fases del ciclo de vida del desarrollo. Esperamos un máximo de 3 por sección, principalmente los de creación inicial o  los de corrección o adición de funcionalidades que consideres más relevantes.
Puedes añadir adicionalmente la conversación completa como link o archivo adjunto si así lo consideras


## Índice

1. [Descripción general del producto](#1-descripción-general-del-producto)
2. [Arquitectura del sistema](#2-arquitectura-del-sistema)
3. [Modelo de datos](#3-modelo-de-datos)
4. [Especificación de la API](#4-especificación-de-la-api)
5. [Historias de usuario](#5-historias-de-usuario)
6. [Tickets de trabajo](#6-tickets-de-trabajo)
7. [Pull requests](#7-pull-requests)

---

## 1. Descripción general del producto

**Prompt 1:** GEMINI - 3.8 Flash
quiero crear un servicio de copiloto para llamadas de voz entrantes a un call center. la idea es que los agentes vean en tiempo real recomendaciones por texto por parte de una IA que este esuchando su conversacion con el cliente llamante. En esta conversacion puede darle informacion inicial del cliente si logra identificarlo y preugntar a servicio API a un CRM, podria odnetificar la opregunta y buscarla, identificar el setimiento de la converscion, darle tips de atencion, etc.

**Prompt 2:** GEMINI - 3.8 Flash
Revisa si los alcances son posibles, buscando tambien ejemplos en la web de servicos similares (bentchmarkt) y que caracteristicas podrian ser de alto valor versus la complejidad de tal manera que podamos tener un PMV en un tiempo moderado y cuales caracteristicas podrias implementarse mas adelante. Evalua tambien si existe un reto tenico insalvable en laactualidad, por ejemplo, la evlocidad de respuesta, manejo del conmtetxo y buffer de voz., y da alternativas de alta usabilidad. En este ejemplo, quiza el agente humano pueda ser el encargado de enviar la informacion con un boton para delimitar el contexto, o crees que pueda ser posible hacerlo en tiempo real sin intervencion de este ultimo? 

**Prompt 3:**
En Wildix hay manera de coenctarse y clonar el  websocket para poder obtener la conversacion en tiempo real? Dame alternativas

**Prompt 4:**
Creo que la opcion eligir es la extension en el navegador. Quiero que me ayudes a completar esta ficha:
0. Ficha del proyecto
0.1. Tu nombre completo:
0.2. Nombre del proyecto:
0.3. Descripción breve del proyecto:
0.4. URL del proyecto:
0.5. URL o archivo comprimido del repositorio
1. Descripción general del producto
Describe en detalle los siguientes aspectos del producto:
1.1. Objetivo:
Propósito del producto. Qué valor aporta, qué soluciona, y para quién.
1.2. Características y funcionalidades principales:
Enumera y describe las características y funcionalidades específicas que tiene el producto para satisfacer las necesidades identificadas.
1.3. Diseño y experiencia de usuario:

---

## 2. Arquitectura del Sistema

### **2.1. Diagrama de arquitectura:**

**Prompt 1:**
Necesito que en base a los conversado me ayudes a llenar los siguientes datos:
2.1. Diagrama de arquitectura:
Usa el formato que consideres más adecuado para representar los componentes principales de la aplicación y las tecnologías utilizadas. Explica si sigue algún patrón predefinido, justifica por qué se ha elegido esta arquitectura, y destaca los beneficios principales que aportan al proyecto y justifican su uso, así como sacrificios o déficits que implica.
2.2. Descripción de componentes principales:
Describe los componentes más importantes, incluyendo la tecnología utilizada
2.3. Descripción de alto nivel del proyecto y estructura de ficheros
Representa la estructura del proyecto y explica brevemente el propósito de las carpetas principales, así como si obedece a algún patrón o arquitectura específica.
2.4. Infraestructura y despliegue
Detalla la infraestructura del proyecto, incluyendo un diagrama en el formato que creas conveniente, y explica el proceso de despliegue que se sigue
2.5. Seguridad
Enumera y describe las prácticas de seguridad principales que se han implementado en el proyecto, añadiendo ejemplos si procede
2.6. Tests
Describe brevemente algunos de los tests realizados

---

### 3. Modelo de Datos

**Prompt 1:**
Ahora genera esta informacion:
3. Modelo de Datos
3.1. Diagrama del modelo de datos:
Recomendamos usar mermaid para el modelo de datos, y utilizar todos los parámetros que permite la sintaxis para dar el máximo detalle, por ejemplo las claves primarias y foráneas.
3.2. Descripción de entidades principales:
Recuerda incluir el máximo detalle de cada entidad, como el nombre y tipo de cada atributo, descripción breve si procede, claves primarias y foráneas, relaciones y tipo de relación, restricciones (unique, not null…), etc.

**Prompt 2:**
Vuelve a Analizar y genera los items de la Sección 3: Modelo de Datos, modelada para una base de datos relacional robusta (como PostgreSQL) que respalda tanto la analítica en caliente como la auditoría post-llamada, control de calidad (QA) y retroalimentación de IA.
---

### 4. Especificación de la API

**Prompt 1:**

4. Especificación de la API

Si tu backend se comunica a través de API, describe los endpoints principales (máximo 3) en formato OpenAPI. Opcionalmente puedes añadir un ejemplo de petición y de respuesta para mayor claridad
---

### 5. Historias de Usuario

**Prompt 1:**
#Rol de la IA
Eres un Product Owner senior con amplia experiencia en soluciones de telefonia y contact center. Tu responsabilidad principal es transformar las respuestas anteriores sobre el producto en un backlog claro y accionable para equipos de desarrollo ágil (Scrum).

#Tarea principal

A partir de lo  visto anteriormente, extrae y redacta historias de usuario en formato estándar y criterios de aceptación verificables, organizados en un backlog inicial.

#Formato exacto de las historias

Para cada historia de usuario:

* Usa estrictamente este formato de frase:

Como [rol], quiero [acción], para [beneficio].

* Añade debajo 3–5 criterios de aceptación en formato Given/When/Then, orientados a comportamiento observable y pruebas funcionales.
  
* Usa la estructura:
  

Given [precondición]

When [acción del usuario o del sistema]

Then [resultado observable verificable]

* Evita criterios genéricos del tipo “la historia funciona correctamente”; cada criterio debe expresar una condición concreta, medible y comprobable.

#Alcance acotado y restricciones (non-goals)

* Solo debes generar historias de usuario para funcionalidades del MVP descritas explícitamente en el alcance anterior.
  
* No inventes features que no estén presentes, aunque te parezcan razonables o típicas de productos similares.
  
* No asignes estimaciones de esfuerzo, tiempos de entrega ni tamaños (no story points, no horas).
  
* No propongas arquitectura, tecnologías, frameworks ni decisiones de implementación.
  
* No reescribas el alcance ni generes documentación adicional; limítate a derivar un backlog de historias de usuario.
  
* No mezcles funcionalidades de fases posteriores (v2, roadmap, nice-to-have) en este backlog; marca claramente el alcance del mvp de la fase 1.
  

#Agrupación de las stories

Agrupa las historias de usuario bajo epígrafes que tengan sentido para el producto, por ejemplo:

* Módulos funcionales .
  
* Casos de uso clave del MVP   
* Épicas si lo puedes estructurar de esa forma.
  
* Elige la forma de agrupación que mejor refleje la estructura real del producto según lo escritoy mantén coherencia en todo el output.
  

#Transparencia y marcado de supuestos

* Cuando tengas que inferir información que no esté escrita literalmente en el PRD (por ejemplo, el rol exacto del usuario o un beneficio que se deduce del contexto), márcalo explícitamente con la etiqueta (asumido) en la historia o en el criterio de aceptación correspondiente.

Ejemplo:

Como coordinador de soporte (asumido), quiero ver el estado de todos los workflows activos, para detectar bloqueos a tiempo (asumido).

* No marques como (asumido) lo que esté claramente especificado de forma literal o inequívoca en el PRD.
  
* Ejemplo de formato de salida esperado
  

A modo de guía, aquí tienes 2 historias de ejemplo (no las reutilices si no encajan con el PRD real, son solo para ilustrar el formato):

Epígrafe: Definición de workflows básicos

Historia de usuario

Como usuario de negocio, quiero crear un workflow con pasos secuenciales, para automatizar un proceso repetitivo de principio a fin.

Criterios de aceptación (Given/When/Then):

Given que tengo acceso a FlowSync y un proyecto activoWhen defino un nuevo workflow con al menos 3 pasos secuencialesThen el sistema guarda el workflow y muestra un identificador único.

Given que el workflow está guardadoWhen abro el detalle del workflowThen veo la lista de pasos en el orden definido originalmente.

Given que el workflow tiene pasos definidosWhen edito el nombre de un paso y guardo los cambiosThen el nuevo nombre se muestra en todos los lugares donde se referencia ese paso.

Historia de usuario

Como usuario avanzado, quiero definir condiciones de transición entre pasos, para que el flujo siga rutas diferentes según el resultado de cada paso.

Criterios de aceptación (Given/When/Then):

Given que estoy editando un workflow existenteWhen añado una condición de transición basada en un campo de resultadoThen el sistema valida la condición y la guarda sin errores.

Given que el workflow tiene al menos dos rutas configuradasWhen se ejecuta el workflow y la condición de transición se cumpleThen la ejecución continúa por la ruta configurada para esa condición.

Given que la condición de transición no se cumpleWhen se ejecuta el workflowThen la ejecución sigue la ruta alternativa o el comportamiento por defecto definido.

* Usa este estilo y estructura para todas las historias derivadas del PRD de FlowSync.

#Instrucción final

Ahora, tomando como entrada el PRD de FlowSync (especialmente la sección que describe el MVP), genera:

* Una lista de epígrafes (módulos, casos de uso o épicas) que representen la estructura funcional del MVP.
  
* Bajo cada epígrafe, las historias de usuario en formato Como [rol], quiero [acción], para [beneficio]. con sus criterios de aceptación Given/When/Then.
  
* Marca con (asumido) cualquier rol, beneficio, condición o detalle que hayas deducido y que no esté literal en el PRD.
  
* Devuélvelo todo en texto plano estructurado con encabezados y listas numeradas, sin incluir comentarios adicionales ni explicaciones fuera del contenido del backlog.
---

### 6. Tickets de Trabajo

**Prompt 1:**
Documenta 3 de los tickets de trabajo principales del desarrollo, uno de backend, uno de frontend, y uno de bases de datos. Da todo el detalle requerido para desarrollar la tarea de inicio a fin teniendo en cuenta las buenas prácticas al respecto.

---

### 7. Pull Requests

**Prompt 1:**

**Prompt 2:**

**Prompt 3:**
