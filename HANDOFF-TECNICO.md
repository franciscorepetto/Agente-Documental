# Agente Documental OCASA — Documento de Handoff Técnico

**Para:** Manuel Pardi  
**De:** Francisco Repetto  
**Fecha:** Junio 2026  

---

## 1. ¿Qué hace el agente?

El Agente Documental es un chatbot que entrevista a un empleado de OCASA y redacta documentos corporativos formales (Procesos, Procedimientos e Instructivos) siguiendo el estilo y estructura real de los documentos de la empresa.

**Flujo resumido:**
1. El usuario configura el documento en un formulario (tipo, área, número, versión, título, autor)
2. El agente hace una entrevista estructurada en ~5 turnos (Objetivo → Alcance → Responsables → Actividades → etc.)
3. Al completar la entrevista, genera el documento completo en texto
4. El usuario descarga el `.docx` formateado con el template de OCASA

---

## 2. Stack actual (prototipo web)

| Componente | Tecnología |
|---|---|
| Frontend | HTML/CSS/JS vanilla — un solo archivo `index.html` |
| LLM | Groq API — modelo `llama-3.3-70b-versatile` |
| Transcripción de audio | Groq Whisper (`whisper-large-v3-turbo`) |
| Generación de .docx | Librería `docx.js` (client-side) |
| Deploy actual | GitHub Pages (estático) |
| Autenticación | Ninguna — la API key la pone cada usuario |

**Lo que NO existe hoy:** backend, base de datos, autenticación, historial persistente.

---

## 3. Parámetros de configuración del documento

Antes de iniciar la conversación, el usuario completa estos campos. En Slack, estos datos deberían recogerse al iniciar el bot (comando `/nuevo-doc` o similar):

| Campo | Valores posibles | Ejemplo |
|---|---|---|
| Tipo de documento | `proceso` / `procedimiento` / `instructivo` | `procedimiento` |
| Código de tipo | `PRO` / `SOP` / `INS` | `SOP` |
| Área responsable | Ver tabla completa en sección 4 | `OPS` |
| Número de documento | Entero 1-9999 (se formatea a 4 dígitos) | `0009` |
| Versión | Entero (se formatea a 2 dígitos, `00` para doc nuevo) | `01` |
| Título del documento | Texto libre, máx. 80 caracteres | `Creación de Solped` |
| Nombre del redactor | Texto libre | `Juan García` |
| Cargo del redactor | Texto libre | `Jefe de Operaciones` |
| Modo | `new` (documento nuevo) / `update` (actualización) | `new` |

**Código generado automáticamente** con el formato:  
`{TIPO} - {AREA} - {NUMERO} - V{VERSION}`  
Ejemplo: `SOP - OPS - 0009 - V01`

---

## 4. Tabla de áreas responsables

| Código | Área |
|---|---|
| OPS | Dirección de Operaciones |
| CX | Gerencia de Customer Experience |
| ADF | Dirección de Administración y Finanzas |
| CDG | Gerencia de Control de Gestión |
| TRF | Gerencia de Transformación |
| CH | Dirección de Capital Humano |
| COM | Dirección Comercial |
| TRP | Gerencia de Estrategia de Transporte |
| CTL | Dirección de Control Operativo |
| IT | Dirección de Informática y Tecnología |
| AAII | Gerencia de Asuntos Institucionales |
| PLA | Gerencia de Planeamiento y Excelencia Operativa |
| HSE | Gerencia Global de Calidad / HSE |
| DIR | Dirección General |
| DDN | Gerencia de Desarrollo de Negocios |
| UEN | Unidad Estratégica de Negocio |
| CMM | Gerencia de Comunicación y Medios |

---

## 5. System prompt completo

El system prompt se construye dinámicamente inyectando los parámetros del paso 3. A continuación el template con los placeholders marcados con `{{}}`:

```
Sos un agente especializado en redacción de documentos corporativos de OCASA Logística General (Argentina). Tu única tarea es ayudar a redactar documentos formales que sigan EXACTAMENTE el estilo, tono y estructura de los documentos reales de OCASA.

TIPO ACTUAL: {{TIPO_LABEL}} ({{TIPO_CODE}}) — FOCO: {{TIPO_FOCO}}
TÍTULO DEL DOCUMENTO (ya definido por el usuario, NO volver a preguntar): "{{TITULO}}"
CÓDIGO: "{{CODIGO}}"
VERSIÓN: "{{VERSION}}"
ÁREA DUEÑA: "{{AREA}}"
REDACTOR: "{{AUTOR_NOMBRE}}" — Cargo: "{{AUTOR_CARGO}}"
MODO: {{MODO}}

━━ ESTILO OCASA ━━
- Tono formal, técnico-operativo, directo. Sin relleno.
- Verbos en MODO IMPERATIVO: "Recibe", "Controla", "Genera", "Valida", "Registra".
- Responsables: nombre del rol en negrita + dos puntos + bullets de responsabilidades.
- Bifurcaciones y casos especiales: SIEMPRE como sub-bullets con letras (a. b. c.), NUNCA texto corrido.
- Notas críticas: "Nota de Control Crítico:" o "Control Crítico:"
- Glosario: sigla en negrita + dos puntos + definición concisa.
- Alcance siempre con "Inicio:" y "Finalización:" explícitos.

━━ ESTRUCTURA OBLIGATORIA ━━
[Para PRO]: OBJETIVO → ALCANCE → RESPONSABLES → GLOSARIO → ACTIVIDADES → VALIDEZ Y REVISIÓN → INDICADORES → DOCUMENTOS RELACIONADOS → ANEXO → HISTORIAL → APROBACIÓN
[Para SOP]: OBJETIVO → ALCANCE → RESPONSABLES → GLOSARIO → ACTIVIDADES → VALIDEZ Y REVISIÓN → INDICADORES → DOCUMENTOS RELACIONADOS → LINKS ÚTILES → HISTORIAL → APROBACIÓN
[Para INS]: OBJETIVO → ALCANCE → RESPONSABLES → GLOSARIO → ACTIVIDADES → INDICADORES → DOCUMENTOS RELACIONADOS → ANEXO → VALIDEZ Y REVISIÓN → HISTORIAL → APROBACIÓN

━━ REGLAS DE COMPORTAMIENTO ━━

FASE 1 — INICIO:
El tipo y título ya fueron definidos por el usuario. NO los vuelvas a preguntar.
Primer mensaje: confirmar tipo+título en una oración, listar las secciones a cubrir, arrancar con Turno 1.

FASE 2 — ENTREVISTA (mínimo 5 turnos):
- UN TURNO = UNA SECCIÓN. Nunca mezclar secciones en el mismo turno.
- MÁXIMO 2 preguntas por turno.
- PROHIBIDO resumir o repetir lo que dijo el usuario. Transición de una sola palabra: "Perfecto." / "Entendido." / "Bien."
- Orden de turnos:
  Turno 1: OBJETIVO
  Turno 2: ALCANCE
  Turno 3: RESPONSABLES
  Turno 4: ACTIVIDADES — estructura
  Turno 5: ACTIVIDADES — detalle y excepciones
  Turno 6 (opcional): GLOSARIO, INDICADORES, DOCUMENTOS RELACIONADOS, LINKS/ANEXO

FASE 3 — GENERACIÓN:
Avisar: "Tengo suficiente información. ¿Querés que genere el documento ahora?"
Al confirmar, generar EXACTAMENTE en este formato:
  Línea 1: "📄 DOCUMENTO GENERADO:"
  Línea 2: "TÍTULO: {titulo}"
  Línea 3: en blanco
  Línea 4+: documento completo empezando con "1. OBJETIVO"

REGLA ABSOLUTA: el bloque del documento termina con la última celda de la tabla de Aprobación. Después: NADA. Cero caracteres. Si hay comentarios al usuario, van ANTES de "📄 DOCUMENTO GENERADO:".
```

---

## 6. Tipos de documento — definición de cada uno

### PRO — Proceso
- **Foco:** Qué y Quién a nivel macro. Flujo de roles y tareas principales sin entrar en detalles operativos mínimos.
- **Actividades:** Secuencia de tareas a nivel MACRO. Para cada paso: ROL + acción principal en modo imperativo.

### SOP — Procedimiento
- **Foco:** Cómo se lleva a cabo una actividad. Paso a paso técnico con puntos de decisión y controles intermedios.
- **Actividades:** Lista numerada. Incluir puntos de decisión, controles y responsables por sección.

### INS — Instructivo
- **Foco:** Ejecución técnica de una tarea muy puntual o el uso de un sistema/herramienta.
- **Actividades:** Cada acción muy específica y secuencial. Modo imperativo.

---

## 7. Señal de fin de documento (parsing)

Para detectar que el LLM terminó de generar el documento, buscar la línea:

```
📄 DOCUMENTO GENERADO:
```

Todo lo que esté **antes** de esa línea es conversación del agente.  
Todo lo que esté **después** hasta el fin del mensaje es el documento.

El documento siempre termina con la tabla de Aprobación:
```
| Revisión | {nombre redactor} | |
| Aprobación | | |
```

---

## 8. Nombre de archivo del documento generado

Formato: `{TITULO} ({TIPO} - {AREA} - {NUMERO}).v{VERSION}.docx`  
Ejemplo: `Creación Solped (SOP - OPS - 0009).v01.docx`

---

## 9. Migración a otro modelo LLM

El agente hoy usa **Groq + Llama 3.3 70B**. Si se migra a **Azure OpenAI (GPT-4o)**, los cambios son mínimos:

- Reemplazar el endpoint `https://api.groq.com/openai/v1/chat/completions` por el de Azure
- El formato de mensajes (`role: system/user/assistant`) es idéntico — OpenAI compatible
- El system prompt no necesita cambios
- Para imágenes (adjuntos), hoy usa `meta-llama/llama-4-scout-17b-16e-instruct` — en Azure sería `gpt-4o` directamente

---

## 10. Consideraciones para implementación en Slack

| Aspecto | Recomendación |
|---|---|
| Inicio de sesión | Comando `/nuevo-doc` que dispara un modal con los campos del paso 3 |
| Thread vs canal | Usar threads — un thread por documento |
| Historial de conversación | Guardar en DB los mensajes del thread para reinyectar en cada llamada al LLM |
| Adjuntos | El usuario puede subir PDF/DOCX/audio — hay que parsearlos antes de enviar al LLM |
| Transcripción de audio | Groq Whisper o Azure Speech-to-Text |
| Descarga del .docx | El bot genera el archivo y lo adjunta como mensaje en el thread |
| Autenticación | Usar la identidad de Slack — el usuario ya está autenticado |

---

## 11. Repositorio

**GitHub:** https://github.com/franciscorepetto/Agente-Documental  
**Rama principal:** `main`  
**Archivo principal:** `index.html`  
